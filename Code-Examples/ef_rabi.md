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

