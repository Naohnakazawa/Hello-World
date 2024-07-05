### EFRabi
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9, -0.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

# ..jupyter-execute::

import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter
from qiskit_experiments.library import EFRabi

with pulse.build() as build_sched:
    pulse.play(
        pulse.Gaussian(160, Parameter("amp"), sigma=40),
        pulse.DriveChannel(0)
    )

exp = EFRabi(
    physical_qubits=(0,),
    backend=backend,
    schedule=build_sched,
    amplitudes=np.linspace(-0.1, 0.1, 21),
)

exp.set_run_options(shots=1000)

exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)
```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/8decc207-b508-4485-b266-5891a3e2d5fa)





While the result of the backend=ibm_kyoto,





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bcb80324-a1ed-4378-b14f-4c1df103a1c2)




What is the difference between the Rabi and the EFRabi experiment?





### EFRabi(revised, backend=STT)
```
# backend
from qiskit_experiments.test import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5e9, -0.25e9, 1e9, 0.8e9, 1e4, noise=True)

# mwc in API document
import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter
from qiskit_experiments.library import EFRabi

with pulse.build() as build_sched:
    with pulse.align_left():
        pulse.shift_frequency(-0.25e9, pulse.DriveChannel(0))
        pulse.play(pulse.Gaussian(160, Parameter("amp"), 40), pulse.DriveChannel(0))

exp = EFRabi(physical_qubits=(0,),
             schedule=build_sched,
             amplitudes=np.linspace(-0.1, 0.1, 21),
             backend=backend,)

display(exp.circuits()[1].draw())
print(exp.circuits()[1].calibrations)

exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)
```







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/373b06fc-8df7-4960-a482-4be18915e10e)








### EFRabi(revised, backend=ibm_kyoto)
```
# backend
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

qubit=67

# mwc
import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter
from qiskit_experiments.library import EFRabi

with pulse.build() as build_sched:
    with pulse.align_left():
        pulse.shift_frequency(- 0.30644e9, pulse.DriveChannel(0))
        pulse.play(pulse.Gaussian(160, Parameter("amp"), 40), pulse.DriveChannel(0))

exp = EFRabi(
    physical_qubits=(qubit,),
    backend=backend,
    schedule=build_sched,
    amplitudes=np.linspace(-0.1, 0.1, 21),
)

exp.set_run_options(shots=1000)

display(exp.circuits()[1].draw())
print(exp.circuits()[1].calibrations)

step1 = True
if step1 == True:
    
    exp_data = exp.run().block_for_results()
    display(exp_data.figure(0))
    exp_data.analysis_results(dataframe=True)

else:
    pass
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bcc2f23d-15bd-49d7-a515-d4af6355dc9d)






