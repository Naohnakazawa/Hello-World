### lab1
```
class GatesPerQubit(AnalysisPass):
    # Your work here - make sure you implement every abstract method defined in `AnalysisPass` (see https://docs.quantum.ibm.com/api/qiskit/qiskit.transpiler.AnalysisPass)

    
    
    
    
    
    
    pass

# my backend with a coupling_map
num_qu = 5
coupling_list = []
for i in range(num_qu-1):
    coupling_list.append([i, i+1])
    coupling_list.append([i+1, i])
    
cm_ex6 = CouplingMap(couplinglist=coupling_list)

backend = GenericBackendV2(num_qubits=num_qu, coupling_map=cm_ex6)
```
