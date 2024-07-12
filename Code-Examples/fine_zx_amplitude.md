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






![image](https://github.com/user-attachments/assets/87fdb425-bc9c-49e9-9d52-84e8bb28d6c5)







TranspilerError: "HighLevelSynthesis was unable to synthesize Instruction(name='szx', num_qubits=2, num_clbits=0, params=[1.5707963267948966])."



### FineZXAmplitude (backend=ibm_kyoto)
```
# backend ibm-kyoto
from qiskit_ibm_provider import IBMProvider

provider = IBMProvider()
backend = provider.get_backend("ibm_kyoto")

qubit=0
for qpair in backend.coupling_map:
    if qpair[0]==qubit:
        qubits=qpair
        break
        
print(qubits)

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
qubits=(0,14,)
inst_map = InstructionScheduleMap()
inst_map.add("szx", qubits, rzx_sched)

repetitions = list(range(15))
exp = FineZXAmplitude(physical_qubits=qubits, backend=backend)
exp.set_experiment_options(repetitions=repetitions)
exp.set_transpile_options(inst_map=inst_map,
                          basis_gates=['cx', 'measure', 'id', 'reset', 'x', 'sx'],
                          optimization_level=3,)

step1=True # run carefully if your device is a real device!
if step1==True:
    
    exp_data=exp.run().block_for_results()
    result=exp_data.analysis_results()

    for _ in result:
        print(_)
        
    exp_data.figure(0)

else:
    pass 
```






![image](https://github.com/user-attachments/assets/4279c229-7b02-4a5d-b515-98194ea6f94c)










TranspilerError: "HighLevelSynthesis was unable to synthesize Instruction(name='szx', num_qubits=2, num_clbits=0, params=[1.5707963267948966])."



