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




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/8027c4de-5ce9-474f-9bc7-9091a0ea356e)






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



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2d7924ab-7901-45cb-8afa-b3bc4eaddbe3)





### qpt
```
# backend 

from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# mwc

from qiskit import QuantumCircuit
from qiskit_experiments.library import ProcessTomography
from qiskit.visualization import plot_state_city


nq = 2
qc_ghz = QuantumCircuit(nq)
qc_ghz.h(0)
qc_ghz.s(0)

for i in range(1, nq):
    qc_ghz.cx(0, i)

qptexp = ProcessTomography(qc_ghz)
qptdata = qptexp.run(backend=backend, shots=1000, seed_simulator=100).block_for_results()
state_result = qptdata.analysis_results("state").value

print(state_result)

# mwc

from qiskit.quantum_info import Statevector, DensityMatrix, Choi
from qiskit.quantum_info.operators import Pauli

import numpy as np

rho_exp = np.array([[4.84417715e-01+0.00000000e+00j, -1.10668939e-03+1.80815509e-03j,
        2.86663380e-03-1.46088240e-03j,  2.20660749e-03-4.51245168e-01j],
                    [-1.10668939e-03-1.80815509e-03j,  2.06261823e-02-1.26009760e-19j,
        9.08274249e-03-4.12631893e-03j, -7.08450542e-03-6.79217904e-03j],
                    [2.86663380e-03+1.46088240e-03j,  9.08274249e-03+4.12631893e-03j,
        1.79174068e-02-5.52265717e-20j,  1.51738418e-03-1.53739746e-02j],
                    [2.20660749e-03+4.51245168e-01j, -7.08450542e-03+6.79217904e-03j,
        1.51738418e-03+1.53739746e-02j,  4.75133966e-01-2.77854849e-19j]])

# mwc

plot_state_city(rho_exp)

```












### mit-ppt
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
from qiskit_experiments.library import MitigatedProcessTomography
from qiskit.visualization import plot_state_city


nq = 2
qc_ghz = QuantumCircuit(nq)
qc_ghz.h(0)
qc_ghz.s(0)

for i in range(1, nq):
    qc_ghz.cx(0, i)

mitqptexp = MitigatedProcessTomography(qc_ghz)
mitqptdata = mitqptexp.run(backend=backend, shots=1000, seed_simulator=100).block_for_results()
state_result = mitqptdata.analysis_results("state").value

print(state_result)

# mwc

from qiskit.quantum_info import Statevector, DensityMatrix, Choi
from qiskit.quantum_info.operators import Pauli

import numpy as np

rho_exp = np.array([
    [5.08935662e-01+0.00000000e+00j,  2.43553818e-02-2.10592660e-03j,
       -6.85747640e-04-1.97912055e-02j, -9.53707413e-03-4.83953470e-01j],
    [2.43553818e-02+2.10592660e-03j,  8.78585177e-03-1.22214326e-19j,
        9.38376732e-04-7.12046649e-03j,  7.16868949e-03-1.61741182e-02j],
    [-6.85747640e-04+1.97912055e-02j,  9.38376732e-04+7.12046649e-03j,
        1.03285720e-02-2.43529561e-19j,  1.36145574e-02+5.50187686e-03j],
    [-9.53707413e-03+4.83953470e-01j,  7.16868949e-03+1.61741182e-02j,
        1.36145574e-02-5.50187686e-03j,  4.71026400e-01-3.06990418e-19j],
])

# mwc

plot_state_city(rho_exp)

```








