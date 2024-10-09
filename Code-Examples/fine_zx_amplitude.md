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








### FineZXAmplitude (revised, backend=DB(2Q, noise-less))
```
# use backend=DynamicsBackend(2Q, noise-less)
# "szx", an instruction for the rzx-gate, is a pulse schedule without cr-tone on the target qubit

# rzx-gate

qubit = 0
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        qubits = qpair
        print(qubits)
        break

print(backend.control_channel(qubits)[0])    

import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter

# calibrated on DB (qubit = 0)

dur = 160
amp = 0.1186741
sigma = 40
beta = -1.618737
angle = 0.0

chan = pulse.DriveChannel(qubit)

with pulse.build(name="x") as x_180p:
    pulse.play(pulse.Drag(duration=dur, amp=amp, sigma=sigma, beta=beta, angle=angle), chan)

# print("The ScheduleBlock of 'x'")
# print(x_180p)

# parameters for CrossResonance tone

tgt_amp = Parameter("tgt_amp")
tgt_angle = Parameter("tgt_angle")
sigma = Parameter("σ")
cr_amp = Parameter("amp")
cr_angle = Parameter("angle")
cr_dur = Parameter("duration")

# values of parameters for CrossResonance tone "cr45p" and "cr45m"
# these values are those of ibm_brisbane, qubits=(18, 19), backend.control_channel(qubits)[0] = ControlChannel(39)

sigma = 32
cr_amp = 0.10362223837368943
# cr_angle = -1.1224743641166546
cr_angle = 0.0
cr_dur = 600
width = 472

t_chan = pulse.DriveChannel(qubits[1])
u_chan = backend.control_channel(qubits)[0]  


with pulse.build(name="cr45p") as cr45p:
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            cr_amp,
            angle=cr_angle,
            width=width,
            sigma=sigma,
        ),
        u_chan,
    )
    
# print("The ScheduleBlock of 'cr45p'")
# print(cr45p)

with pulse.build(name="cr45m") as cr45m:
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            cr_amp,
            angle=cr_angle + np.pi,
            width=width,
            sigma=sigma,
        ),
        u_chan,
    )

# print("The ScheduleBlock of 'cr45m'")
# print(cr45m)

with pulse.build(name="szx") as your_rzx:
    with pulse.align_sequential():
        pulse.reference("cr45p", "q0", "q1")
        pulse.reference("x", "q0")
        pulse.reference("cr45m", "q0", "q1")
        pulse.reference("x", "q0")

your_rzx.assign_references(subroutine_dict={("cr45p", "q0", "q1"): cr45p})
your_rzx.assign_references(subroutine_dict={("x", "q0"): x_180p})
your_rzx.assign_references(subroutine_dict={("cr45m", "q0", "q1"): cr45m})
               
print("The ScheduleBlock of 'your_rzx'")
display(your_rzx.draw())

# mwc
from qiskit_experiments.library import FineZXAmplitude

exp = FineZXAmplitude(physical_qubits=(0, 1), backend=backend)

# %time 
exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
```









![image](https://github.com/user-attachments/assets/a27e79c0-a435-493f-a7ce-be4c90bcfc8e)










![image](https://github.com/user-attachments/assets/b9fe87f7-ca17-45f0-9a98-2f53e578cf79)













