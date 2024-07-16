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
# backend
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

qubit=12
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        qubits=qpair
        print(qubits)
        break

qubits=(12, 17)

# mwc
import numpy as np
import qiskit.pulse as pulse
from qiskit.circuit import QuantumCircuit
from qiskit.circuit.library import RZXGate
from qiskit.compiler import schedule, transpile
from qiskit.pulse import Schedule, ScheduleBlock, InstructionScheduleMap
from qiskit_experiments.library import FineZXAmplitude

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

qc_t = transpile(qc, backend=backend, optimization_level=3, initial_layout=[12, 17])
rzx_sched = schedule(qc_t, backend=backend)
display(transpiled_rzx_sched.draw())

inst_map = InstructionScheduleMap()
inst_map.add("szx", qubits, rzx_sched)

repetitions = list(range(15))
exp = FineZXAmplitude(physical_qubits=qubits, backend=backend)
exp.set_experiment_options(repetitions=repetitions)
exp.set_transpile_options(inst_map=inst_map,
                          basis_gates=["id","rz","sx","x","cx","reset","delay","measure"],
                          unitary_synthesis_method="default",
                          optimization_level=1)
exp.set_run_options(shots=100)

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






![image](https://github.com/user-attachments/assets/f369aff1-a0e4-46d1-b270-083246c0aed9)








TranspilerError: "HighLevelSynthesis was unable to synthesize Instruction(name='szx', num_qubits=2, num_clbits=0, params=[])."





### FineZXAmplitude (backend=ibm_brisbane)
```
# backend
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_brisbane")

qubit=12
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        qubits=qpair
        print(qubits)
        break

qubits=(12, 17)

...

```




![image](https://github.com/user-attachments/assets/4d076c6b-01dc-491a-acf8-408c636e30a8)












### The index of a ControlChannel
```
# Qiskit pulse

a0 = pulse.AcquireChannel(0)
u0 = pulse.ControlChannel(0)
d0 = pulse.DriveChannel(0)
m0 = pulse.MeasureChannel(0)

with pulse.build() as sched:
    pulse.play(np.linspace(0,1,100), d0)
    pulse.play(np.linspace(1,0,100), d0)
    pulse.barrier(d0, m0)
    pulse.play(pulse.Gaussian(200, 0.5, 20), m0)

sched.draw()

with pulse.build(schedule=sched) as sched:
    pulse.shift_frequency(1e9, d0)
    pulse.shift_phase(np.pi, m0)
    
sched.draw()

# measurement schedule
#
#n The acquirechannel does not appear!

mem_slot = pulse.MemorySlot(0)

with pulse.build() as meas_sched:
    with pulse.align_right():        
        pulse.call(sched)
        pulse.acquire(200, a0, mem_slot)
    
meas_sched.draw()
```







![image](https://github.com/user-attachments/assets/375cec01-4512-4d64-baee-5cd892a55816)








