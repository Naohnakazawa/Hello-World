### qpt (CRGate, backend=Aer(FakePerth))
```
# code example 20241017

# a fidelity test of 2-qubits gates, cx(), ecr(),...

# backend 
from qiskit_aer import AerSimulator
from qiskit_ibm_runtime.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

num_qubits = 2
shots=100000
seed=100
# mwc

import numpy as np
from qiskit import QuantumCircuit
from qiskit_experiments.library import ProcessTomography

qc_cx = QuantumCircuit(num_qubits)

for i in range(1, num_qubits):
    qc_cx.cx(0, i)

qptexp = ProcessTomography(qc_cx)
qptdata = qptexp.run(backend=backend, shots=shots, seed_simulator=seed).block_for_results()
choi_out = qptdata.analysis_results("state").value

# extracting a densitymatrix from choi_out

# 00

from qiskit.visualization import plot_state_city
import qiskit.quantum_info as qinfo

_rho_exp_00 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_00[i][j] = choi_out.data[i][j]

# experimental
rho_exp_00 = qinfo.DensityMatrix(_rho_exp_00)       

display(plot_state_city(rho_exp_00, title="rho_exp_00"))
print(f" The purity of the state rho_exp_00 is {rho_exp_00.purity()}.")

# ideal
rho_ideal_00 = qinfo.DensityMatrix.from_instruction(qc_cx)

display(plot_state_city(rho_ideal_00, title="rho_ideal_00"))
print(f" The purity of the state rho_ideal_00 is {rho_ideal_00.purity()}.")

# fidelity
fidelity_00 = qinfo.state_fidelity(rho_exp_00, rho_ideal_00, validate=False)
print(f" The fidelity of rho_exp_00 is {fidelity_00}.")

# 01

_rho_exp_01 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_01[i][j] = choi_out.data[i+4][j+4]

# experimental
rho_exp_01 = qinfo.DensityMatrix(_rho_exp_01)

display(plot_state_city(rho_exp_01, title="rho_exp_01"))
print(f" The purity of the state is {rho_exp_01.purity()}.")

# ideal
qc_cx_01 = QuantumCircuit(num_qubits)
qc_cx_01.x(0)

for i in range(1, num_qubits):
    qc_cx_01.cx(0, i)

rho_ideal_01 = qinfo.DensityMatrix.from_instruction(qc_cx_01)

display(plot_state_city(rho_ideal_01, title="rho_ideal_01"))
print(f" The purity of the state rho_ideal_01 is {rho_ideal_01.purity()}.")

fidelity_01 = qinfo.state_fidelity(rho_exp_01, rho_ideal_01, validate=False)
print(f" The fidelity of the state rho_ideal_01 is {fidelity_01}.")


# 10

_rho_exp_10 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_10[i][j] = choi_out.data[i+8][j+8]

# experimental
rho_exp_10 = qinfo.DensityMatrix(_rho_exp_10)

display(plot_state_city(rho_exp_10, title="rho_exp_10"))
print(f" The purity of the state is {rho_exp_10.purity()}.")

# ideal
qc_cx_10 = QuantumCircuit(num_qubits)
qc_cx_10.x(1)

for i in range(1, num_qubits):
    qc_cx_10.cx(0, i)

rho_ideal_10 = qinfo.DensityMatrix.from_instruction(qc_cx_10)

display(plot_state_city(rho_ideal_10, title="rho_ideal_10"))
print(f" The purity of the state rho_ideal_10 is {rho_ideal_10.purity()}.")

fidelity_10 = qinfo.state_fidelity(rho_exp_10, rho_ideal_10, validate=False)
print(f" The fidelity of the state rho_ideal_10 is {fidelity_10}.")


# 11

_rho_exp_11 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_11[i][j] = choi_out.data[i+12][j+12]

# experimental
rho_exp_11 = qinfo.DensityMatrix(_rho_exp_11)

display(plot_state_city(rho_exp_11, title="rho_exp_11"))
print(f" The purity of the state is {rho_exp_11.purity()}.")

# ideal
qc_cx_11 = QuantumCircuit(num_qubits)
qc_cx_11.x(0)
qc_cx_11.x(1)

for i in range(1, num_qubits):
    qc_cx_11.cx(0, i)

rho_ideal_11 = qinfo.DensityMatrix.from_instruction(qc_cx_11)

display(plot_state_city(rho_ideal_11, title="rho_ideal_11"))
print(f" The purity of the state rho_ideal_11 is {rho_ideal_11.purity()}.")

fidelity_11 = qinfo.state_fidelity(rho_exp_11, rho_ideal_11, validate=False)
print(f" The fidelity of the state rho_ideal_11 is {fidelity_11}.")
```







![image](https://github.com/user-attachments/assets/770a6183-83c5-4d0f-8c73-027af451d7ef)






For figures, see QPT-CRGate-Aer(FakePerth)-v100.ipynb.
