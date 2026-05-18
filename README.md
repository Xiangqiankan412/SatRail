# SatRail
This is data from the paper T-CDRA: Operation-Driven LEO Constellation Design and Resource Allocation for Train Communications, which is my second project on GitHub.

## 1. Obtaining Train Running Data

Taking 17 trains running horizontally and vertically across China as examples, 17 tables are used to record the longitude and latitude information of the 17 trains. 

Specifically, path interpolation is performed between two adjacent stations to simulate the complete longitude and latitude information of the railway line. Geographically, a 1-degree change in latitude corresponds to approximately 111 km, while a 1-degree change in longitude corresponds to approximately 85–105 km. Since the time step in the constellation design code is 60 seconds, the train position is updated once at each time step. The latitude and longitude of a train change by 0.045 degrees between two adjacent time steps, which corresponds to a train speed of approximately 280 km/h.

---
## 2. Constellation Design

During the constellation design phase, the time step is 60 seconds and the operation period is 24 hours. Therefore, there are 1440 time steps in total.

The trajectory information of the 17 trains is integrated into the file `Trains_1440_Steps.xlsx`, which contains 1440 sheets. The same cell position in adjacent sheets represents the same train. After a train arrives at its terminal station, its latitude and longitude remain fixed at the terminal station.The constellation design needs to overwrite the longitude and latitude information in the corresponding sheet at each time step.

The constellation design results are stored in the file `pareto_population_decoded.xlsx`, which contains the constellation design variables, corresponding to those defined in the paper.

## 3. Construction of the Satellite-Railway Network Knowledge Graph

Four constellations, namely A, B, C, and D, are selected from the constellation design results. Taking constellation C as an example, which consists of `4 × 6 = 24` satellites, this is the constellation used for the 2D and 3D visualization in the paper.

The access files of the constellation are exported from STK. The topology connection matrix files are generated using a 90-second time slice interval, as shown in the `access` folder. For each time slice, a satellite-railway network knowledge graph is constructed. The knowledge graph is recorded in the form of triples.

Taking the topology connection matrix `matrix_step_0000_04_00_00.xlsx` as an example, this topology only contains the connection information between satellite nodes. Therefore, the connection information between satellites and trains, as well as between satellites and gateway stations, needs to be supplemented into `matrix_step_0000_04_00_00.xlsx`. Based on the topology connection matrix `matrix_step_0000_04_00_00.xlsx`, positive triples can be extracted. All triple information is written into the file `slice_0000_04_00_00.xlsx`.

All satellite nodes, train nodes, gateway station nodes, links, and tasks are treated as entities and written into the file `entity.txt`.

---

## 4. KGE Embedding

The files `entity.txt` and `slice_0000_04_00_00.xlsx` are used as inputs to the knowledge graph embedding code. After running the embedding code, the normalized embedding vectors of entities and relations are obtained and saved in `Embedded_vector.txt`.

---

## 5. Resource Allocation

The inputs of the resource allocation code `KA_RA.py` include not only the embedding vector file `Embedded_vector.txt` obtained from KG embedding, but also the following files:

- `node.xlsx`
- `link.xlsx`
- `train_task_set.xlsx`
- `node_emb.xlsx`
- `link_emb.xlsx`
- `task_emb.xlsx`

Among them, `node.xlsx` and `link.xlsx` contain the resource information of nodes and links, respectively. The file `train_task_set.xlsx` contains the task set. The files `node_emb.xlsx`, `link_emb.xlsx`, and `task_emb.xlsx` contain the embedding information extracted from `Embedded_vector.txt`.

By running the resource allocation code `KA_RA.py`, the result file `KA_RA_results.xlsx` can be obtained. This file records detailed information such as task requirements and routing results.

---

## Summary

The above experimental procedure provides a brief description of the experiments conducted in the paper.

In fact, this work involves a considerable amount of effort. First, the longitude and latitude information of trains at each time step needs to be obtained. Then, constellation design is performed, and four constellations are selected from a large number of candidate constellations. Next, these four constellations are constructed in STK to obtain the inter-satellite connection relationships. The results are processed into time-sliced connection matrices. Meanwhile, based on the constellation parameters, the satellites connected to trains and gateway stations at different time slices are calculated. This information is then supplemented to obtain the complete time-sliced connection matrices of the satellite-railway network. Finally, task routing is performed for each constellation at each time slice. The cost is accumulated over all time slices to obtain the total cost of the constellation during the operation period, followed by experimental analysis.
