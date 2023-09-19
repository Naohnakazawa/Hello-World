# Tomography experiments

### qst
```
# backend 

from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# mwc

from qiskit import QuantumCircuit
from qiskit_experiments.library import StateTomography
from qiskit.visualization import plot_state_city


nq = 2
qc_ghz = QuantumCircuit(nq)
qc_ghz.h(0)
qc_ghz.s(0)

for i in range(1, nq):
    qc_ghz.cx(0, i)

qstexp = StateTomography(qc_ghz)
qstdata = qstexp.run(backend=backend, shots=1000, seed_simulator=100).block_for_results()
state_result = qstdata.analysis_results("state")
plot_state_city(state_result.value, title="Density Matrix")

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/98a5421d-2a75-4035-8198-a7c9793dce94)





### mit-qst
```
# backend 

from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakePerth(), 
                                      thermal_relaxation=False, 
                                      gate_error=False, 
                                      readout_error=True, 
                                     )

backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

# mwc

from qiskit import QuantumCircuit
from qiskit_experiments.library import MitigatedStateTomography
from qiskit.visualization import plot_state_city


nq = 2
qc_ghz = QuantumCircuit(nq)
qc_ghz.h(0)
qc_ghz.s(0)

for i in range(1, nq):
    qc_ghz.cx(0, i)

mitqstexp = MitigatedStateTomography(qc_ghz)
mitqstdata = mitqstexp.run(backend=backend, shots=1000, seed_simulator=100).block_for_results()
state_result = mitqstdata.analysis_results("state")
plot_state_city(state_result.value, title="Density Matrix")

```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/dcbca5dd-2c24-41aa-9760-c0393bc5f42e)


