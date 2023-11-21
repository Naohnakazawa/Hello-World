# Quantum volume experiments

### QV
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit import Aer
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakeSydneyV2
backend = AerSimulator.from_backend(FakeSydneyV2())

# ..jupyter-execute::

from qiskit_experiments.framework import BatchExperiment
from qiskit_experiments.library import QuantumVolume

qubits = tuple(range(4)) # Can use specific qubits. for example [2, 4, 7, 10]
qv_exp = QuantumVolume(qubits, seed=42)
qv_exp.set_transpile_options(optimization_level=3)

expdata = qv_exp.run(backend).block_for_results()

display(expdata.figure(0))

for result in expdata.analysis_results():
    print(result)
```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/6ad78dee-4fa8-47ab-8e8f-cae1c99de1db)





AnalysisResult
- name: mean_HOP
- value: 0.74+/-0.04
- quality: bad
- extra: <4 items>
- device_components: ['Q0', 'Q1', 'Q2', 'Q3']
- verified: False
AnalysisResult
- name: quantum_volume
- value: 1
- quality: bad
- extra: <4 items>
- device_components: ['Q0', 'Q1', 'Q2', 'Q3']
- verified: False


