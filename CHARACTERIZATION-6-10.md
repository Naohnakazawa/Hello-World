# Characterization experiments 6,7,8,9,10

### Rabi
```
# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

seed = 200
backend = SingleTransmonTestBackend(5.2e9, seed=seed)

# mwc
import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter
from qiskit_experiments.library import Rabi

with pulse.build() as build_sched:
    pulse.play(pulse.Gaussian(160, Parameter("amp"), 40), pulse.DriveChannel(0))

exp = Rabi(physical_qubits=(0,),
           schedule=build_sched,
           amplitudes=np.linspace(-0.1, 0.1, 21),
           backend=backend,)

exp_data = exp.run().block_for_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/93ee2680-b639-4059-b320-71cb3a68cbb8)







### RoughDrag
```
# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

seed=101
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=seed)

# mwc
import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter
from qiskit_experiments.library import RoughDrag

with pulse.build() as build_sched:
    pulse.play(pulse.Drag(160, 0.50, 40, Parameter("beta")), pulse.DriveChannel(0))

exp = RoughDrag(physical_qubits=(0,), 
                schedule=build_sched,
                betas = np.linspace(-4, 4, 51), 
                backend=backend,)
exp.set_experiment_options(reps=[3, 5, 7])

exp_data = exp.run().block_for_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/e2261a84-2e33-4879-ba9e-8c65158b72a1)







### FineAmplitude
```
# backend
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
seed=101
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=seed)

# mwc
import numpy as np
import qiskit.pulse as pulse
# from qiskit.pulse import InstructionScheduleMap
from qiskit_experiments.library import FineXAmplitude

qubit = 0
x_pulse = backend.defaults().instruction_schedule_map.get('x', (qubit,)).instructions[0][1].pulse
# drive_chan = pulse.DriveChannel(qubit)
# inst_map = pulse.InstructionScheduleMap()

ideal_amp = x_pulse.amp
over_amp = ideal_amp*1.01
with pulse.build(backend=backend, name="x") as x_over:
    pulse.play(pulse.Drag(x_pulse.duration, over_amp, x_pulse.sigma, x_pulse.beta),
               pulse.DriveChannel(qubit),)
inst_map.add("x", (qubit,), x_over) 

exp = FineXAmplitude(qubit, backend=backend)
# exp.set_transpile_options(inst_map=inst_map)
exp_data = exp.run().block_for_results()
exp_data.figure(0)
```

 



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2b007bcf-f5b9-4fd7-9df9-3db701428c94)







### EFSpectroscopy
```
# backend
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# mwc
import numpy as np
from qiskit_experiments.library.characterization import EFSpectroscopy

qubit = 0
freq01_estimate = backend.defaults().qubit_freq_est[qubit]
frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)

exp = EFSpectroscopy(physical_qubits = (0,),
                         frequencies = frequencies, 
                         backend = backend, 
                         absolute = True, 
                        )
exp.set_experiment_options(amp=0.005)  # This is not a default value

exp_data = exp.run().block_for_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/bef91275-c98f-49ef-a1d4-7d5f72c1b33c)







### EFRabi
```
# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

seed = 200
backend = SingleTransmonTestBackend(5.2e9, seed=seed)

# mwc
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

exp_data = exp.run().block_for_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a5a30a88-f029-4eae-b01c-fa0717d1c413)






