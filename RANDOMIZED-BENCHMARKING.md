# Randomized benchmarking experiments

### StandardRB
```
# backend 
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# mws
import numpy as np
from qiskit_experiments.library import StandardRB
import qiskit.circuit.library as circuits

lengths = np.arange(1, 800, 200)
num_samples = 10
seed = 1010
qubits = [0]

exp = StandardRB(physical_qubits=[0],
                 lengths=np.arange(1, 800, 200),
                 num_samples=10, 
                 backend=backend, 
                 seed=1010, )
expdata = exp.run().block_for_results()
results = expdata.analysis_results()

print("Gate error ratio : %s" % expdata.experiment.analysis.options.gate_error_ratio)
display(expdata.figure(0))

```
Gate error ratio : {'x': 1.0, 'rz': 0.0, 'sx': 1.0}





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/0804e1cc-b3dd-436a-9f58-2ba74bfa0172)








