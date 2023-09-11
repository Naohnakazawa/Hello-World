# Hello-World

This is **Naohito Nakazawa's** first repository on github. *I am*

## *a physicist* 
### a part-time lecturer in Waseda University

a begnner of github and also, 
a begnner of python

This is a <sub>subscript</sub> text.

This is a <sup>superscript</sup> test.

Today is a **holiday**.
> Today is a **holiday**.

Some basic Git commands are:
```
git status
git add
git diff 
git commit
git log 
git checkout # dangerous!
git restore  # dangerous!
```

An interesting paper is [here](https://arxiv.org/abs/2308.02321).





### This is a figure of T1-experiment:

(The figure is dragged from the local jupyter notebook and dropped on this private repository.)
```
from qiskit_experiments.library import T1
from qiskit.providers.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
import numpy as np

backend = AerSimulator.from_backend(FakeManilaV2())

qubit0_t1 = FakeManilaV2().qubit_properties(0).t1
delays = np.arange(1.00e-6, 3*qubit0_t1, 30.0e-6)

exp = T1(physical_qubits=(0, ), delays=delays)
exp_data = exp.run(backend=backend).block_for_results()
display(exp_data.figure(0))
```







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/872469ba-6668-49ad-b05f-afb6907f9e2e)








### This is a figure of T2Hahn-experiment:

```
from qiskit_experiments.library.characterization.t2hahn import T2Hahn

conversion_factor = 1e-6
delays = list(range(0, 50, 1))
delays = [float(_)*conversion_factor for _ in delays]
number_of_echoes = 1

exp = T2Hahn(physical_qubits=(0, ), delays=delays, num_echoes=number_of_echoes)
print(exp.circuits()[0])

from qiskit_experiments.test.t2hahn_backend import T2HahnBackend

estimated_t2hahn = 20*conversion_factor

backend = T2HahnBackend(
    t2hahn=[estimated_t2hahn], 
    frequency=[100100], 
    readout0to1 = [0.02], 
    readout1to0 = [0.02], 
    )

exp.analysis.set_options(p0=None, plot=True)
expdata = exp.run(backend=backend, shots=1000, seed_simulator=101)
expdata.block_for_results()

display(expdata.figure(0))

```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/96440a20-c0e8-4582-9ed0-438e167b9c73)









### T"*-Ramsey :

```
from qiskit_experiments.library import T2Ramsey
import numpy as np

delays = list(np.arange(1.00e-6, 50.0e-6, 2.00e-6))
exp = T2Ramsey(physical_qubits=(0, ), delays=delays, osc_freq=1.0e5)

from qiskit.providers.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakeManilaV2(), 
                                      thermal_relaxation=True, 
                                      gate_error=False, 
                                      readout_error=False,                                           
)

backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

exp.set_transpile_options(scheduling_method="asap")

expdata = exp.run(backend=backend, shots=1000, seed_simulator=101)
expdata.block_for_results()

display(expdata.figure(0))

```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/07f3ea93-1f28-4997-8d18-fa7935f9f1a3)









### Tphi-experiment

```
from qiskit_experiments.library.characterization import Tphi
import numpy as np

from qiskit.providers.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakeManilaV2(), 
                                      thermal_relaxation=True, 
                                      gate_error=False, 
                                      readout_error=False, 
                                     )

backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

delays_t1 = np.arange(1e-6, 300e-6, 10e-6)
delays_t2 = np.arange(1e-6, 50e-6, 2e-6)

exp = Tphi(physical_qubits=(0, ), delays_t1=delays_t1, delays_t2=delays_t2, num_echoes=1)

expdata = exp.run(backend=backend, seed_simulator=100).block_for_results()
result = expdata.analysis_results("T_phi")

```

AnalysisResult
- name: T_phi
- value: (5.15+/-0.31)e-06
- quality: good
- extra: <1 items>
- device_components: ['Q0']
- verified: False



### QV
```
from qiskit_experiments.framework import BatchExperiment
from qiskit_experiments.library import QuantumVolume
from qiskit import Aer
from qiskit_aer import AerSimulator

# For simulation
from qiskit.providers.fake_provider import FakeSydneyV2

backend = AerSimulator.from_backend(FakeSydneyV2())

qubits = tuple(range(4)) # Can use specific qubits. for example [2, 4, 7, 10]

qv_exp = QuantumVolume(qubits, seed=42)
# Transpile options like optimization_level affect only the real device run and not the simulation run
# Run options affect both simulation and real device runs
qv_exp.set_transpile_options(optimization_level=3)

# Run experiment
expdata = qv_exp.run(backend).block_for_results()

# View result data
display(expdata.figure(0))

for result in expdata.analysis_results():
    print(result)
```





![image](https://qiskit.org/ecosystem/experiments/dev/_images/quantum_volume_2_0.png)





### QS
```
import numpy as np
from qiskit_experiments.library.characterization import QubitSpectroscopy

# backend

from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

# mwc

qubit = 0
freq01_estimate = backend.defaults().qubit_freq_est[qubit]
frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)

spec = QubitSpectroscopy(physical_qubits = (0,),
                         frequencies = frequencies, 
                         backend = backend, 
                         absolute = True, 
                        )
spec.set_experiment_options(amp=0.005)  # This is not a default value

# mwc

spec_data = spec.run().block_for_results()
spec_data.figure(0)

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/fafdf88a-6616-4040-83d8-bd8798b7133a)









### Rabi
```
# mwc

import numpy as np

from qiskit import pulse
from qiskit.circuit import Parameter

# backend 

from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

seed = 100
backend = SingleTransmonTestBackend(seed=seed)

# mwc
from qiskit_experiments.library import Rabi

with pulse.build() as sched:
    pulse.play(
        pulse.Gaussian(160, Parameter("amp"), sigma=40),
        pulse.DriveChannel(0)
    )

rabi = Rabi(
    physical_qubits=(0,),
    backend=backend,
    schedule=sched,
    amplitudes=np.linspace(-0.1, 0.1, 21),
)

rabi_data = rabi.run().block_for_results()
rabi_data.figure(0)

```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/3c39fc12-e7da-4cfc-9c72-22dc419ed982)







### RoughDrag
```
# mwc

import numpy as np

from qiskit_experiments.library import RoughDrag
from qiskit_experiments.visualization import PlotStyle
from qiskit.circuit import Parameter
from qiskit import pulse
from qiskit.pulse import DriveChannel, Drag

beta = Parameter("beta")
with pulse.build(name="xp") as xp:
    pulse.play(pulse.Drag(160, 0.50, 40, beta), pulse.DriveChannel(0))

# backend 

from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend

seed=100
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=seed)

# mwc

drag = RoughDrag(physical_qubits=(0,), schedule=xp, backend=backend)
drag.set_experiment_options(reps=[3, 5, 7])
drag_data = drag.run().block_for_results()
drag_data.figure(0)

```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2346363d-bf6a-4072-aced-b70879cb11eb)







### FineAmplitude
```
# mwc

import numpy as np
import qiskit.pulse as pulse
from qiskit.pulse import InstructionScheduleMap
from qiskit_experiments.library import FineXAmplitude

# backend

from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)
qubit = 0

# mwc

x_pulse = backend.defaults().instruction_schedule_map.get('x', (qubit,)).instructions[0][1].pulse
d0 = pulse.DriveChannel(qubit)
inst_map = pulse.InstructionScheduleMap()

ideal_amp = x_pulse.amp
over_amp = ideal_amp*1.01
with pulse.build(backend=backend, name="x") as x_over:
    pulse.play(pulse.Drag(x_pulse.duration, over_amp, x_pulse.sigma, x_pulse.beta), d0)
inst_map.add("x", (qubit,), x_over) 

overamp_exp = FineXAmplitude(qubit, backend=backend)
overamp_exp.set_transpile_options(inst_map=inst_map)

# mwc

exp_data_over = overamp_exp.run(backend).block_for_results()
exp_data_over.figure(0)

```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/c920cf7d-1722-4567-b0a5-3b08f5f4f20a)






### LocalReadoutError
```
# mwc

import numpy as np
import matplotlib.pyplot as plt
from qiskit import QuantumCircuit
from qiskit.visualization import plot_histogram
from qiskit_experiments.library import LocalReadoutError

# backend 

from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# mwc

shots=1000
qubits = list(range(4))
num_qubits = 4

exp = LocalReadoutError(physical_qubits=qubits)

exp.analysis.set_options(plot=True)
result = exp.run(backend=backend)
mitigator = result.analysis_results(0).value

# mwc

result.figure(0)

```
















