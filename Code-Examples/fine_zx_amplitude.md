### FineZXAmplitude
```
# backend
from qiskit.providers.fake_provider import GenericBackendV2
num_qubits=5

backend = GenericBackendV2(num_qubits=num_qubits, calibrate_instructions=True)

# mwc is as follows

import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import QuantumCircuit
from qiskit.circuit.library import RZXGate
from qiskit.compiler import schedule, transpile
from qiskit.pulse import InstructionScheduleMap
from qiskit_experiments.library import FineZXAmplitude

# definition of RZXGate(np.pi/2)
print(RZXGate(np.pi/2).decompositions[0])

# decomposition of RZXGate(np.pi/2) and the pulse schedule
qc=QuantumCircuit(2)

qc.rz(np.pi/2, 1)
qc.sx(1)
qc.rz(np.pi/2, 1)

qc.cx(0, 1)
qc.rz(np.pi/2, 1)
qc.cx(0, 1)

qc.rz(np.pi/2, 1)
qc.sx(1)
qc.rz(np.pi/2, 1)

qc.draw("mpl")

# palse schedule
qc_t = transpile(qc, backend, optimization_level=3)
rzx_sched = schedule(qc_t, backend=backend)

display(rzx_sched.draw())

# FineZXAmplitude experiment

qubits=(0,1,)
inst_map = InstructionScheduleMap()
inst_map.add("szx", qubits, rzx_sched)

repetitions = list(range(15))
exp = FineZXAmplitude(physical_qubits=qubits, backend=backend)
exp.set_experiment_options(repetitions=repetitions)
exp.set_transpile_options(inst_map=inst_map, basis_gates=['cx', 'measure', 'id', 'reset', 'x', 'sx'])

exp_data = exp.run()
```





