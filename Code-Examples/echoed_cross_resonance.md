### EchedCrossResonance
```
# backend

from qiskit_ibm_runtime.fake_provider import FakePerth
backend = FakePerth()

print(backend.dt)
# print(backend.coupling_map)

print(f"num_qubits = {backend.num_qubits}")
qubit = 3
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        qubits = qpair
        break

print(qubits)
print(backend.control_channel(qubits)[0])

# mwc

import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter

# default values of parameters for x-gate

dur = 120
amp = 0.19248967903336803
sigma = 30
beta = 0.5955879159257184
angle = 0.0

chan = pulse.DriveChannel(qubit)

with pulse.build(name="x") as x_180p:
    pulse.play(pulse.Drag(duration=dur, amp=amp, sigma=sigma, beta=beta, angle=angle), chan)

print("The ScheduleBlock of 'x'")
print(x_180p)

# parameters for CrossResonance tone

tgt_amp = Parameter("tgt_amp")
tgt_angle = Parameter("tgt_angle")
sigma = Parameter("σ")
cr_amp = Parameter("amp")
cr_angle = Parameter("angle")
cr_dur = Parameter("duration")

# values of parameters for CrossResonance tone "cr45p" and "cr45m"
# these values are those of ibm_brisbane, qubits=(18, 19), backend.control_channel(qubits)[0] = ControlChannel(39)

tgt_amp = 0.036750312295787485
tgt_angle = 0.02117256653723946
sigma = 32
cr_amp = 0.10362223837368943
cr_angle = -1.1224743641166546
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
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            tgt_amp,
            angle=tgt_angle,
            width=width,
            sigma=sigma,
        ),
        t_chan,
    )

    
print("The ScheduleBlock of 'cr45p'")
print(cr45p)

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
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            tgt_amp,
            angle=tgt_angle + np.pi,
            width=width,
            sigma=sigma,
        ),
        t_chan,
    )

    
print("The ScheduleBlock of 'cr45m'")
print(cr45m)

with pulse.build(name="ecr") as ecr:
    with pulse.align_sequential():
        pulse.reference("cr45p", "q0", "q1")
        pulse.reference("x", "q0")
        pulse.reference("cr45m", "q0", "q1")

ecr.assign_references(subroutine_dict={("cr45p", "q0", "q1"): cr45p})
ecr.assign_references(subroutine_dict={("x", "q0"): x_180p})
ecr.assign_references(subroutine_dict={("cr45m", "q0", "q1"): cr45m})
               
print("The ScheduleBlock of 'ecr'")
print(ecr)

display(cr45p.draw())
display(x_180p.draw())
display(cr45m.draw())
display(ecr.draw())

with pulse.build(name="rzx") as rzx:
    with pulse.align_sequential():
        pulse.reference("cr45p", "q0", "q1")
        pulse.reference("x", "q0")
        pulse.reference("cr45m", "q0", "q1")
        pulse.reference("x", "q0")

rzx.assign_references(subroutine_dict={("cr45p", "q0", "q1"): cr45p})
rzx.assign_references(subroutine_dict={("x", "q0"): x_180p})
rzx.assign_references(subroutine_dict={("cr45m", "q0", "q1"): cr45m})
               
print("The ScheduleBlock of 'rzx'")
print(rzx)
display(rzx.draw())
```






![image](https://github.com/user-attachments/assets/e09d01cf-8668-446a-bbd8-5bbb7cc89dae)








![image](https://github.com/user-attachments/assets/55c0a8b4-11a4-4746-90b1-c2d9de5813a1)







