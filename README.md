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





### T1-experiment:

(The figure is dragged from the local jupyter notebook and dropped on this private repository.)
```
# backend 
from qiskit.providers.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
backend = AerSimulator.from_backend(FakeManilaV2())

# mwc
from qiskit_experiments.library import T1
import numpy as np

### qubit0_t1 = FakeManilaV2().qubit_properties(0).t1
### delays = np.arange(1.00e-6, 3*qubit0_t1, 30.0e-6)

delays = np.arange(1.e-6, 300.e-6, 30.e-6)
exp = T1(physical_qubits=(0, ), delays=delays, backend=backend)

# mwc
exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/da71acf1-d516-4470-a5c9-eec1b7492d0d)






### T2Hahn-experiment:

```
# backend
from qiskit_experiments.test.t2hahn_backend import T2HahnBackend

conversion_factor = 1e-6
estimated_t2hahn = 20*conversion_factor
backend = T2HahnBackend(
    t2hahn=[estimated_t2hahn], 
    frequency=[100100], 
    readout0to1 = [0.02], 
    readout1to0 = [0.02], 
    )

# mwc
import qiskit
from qiskit_experiments.library.characterization.t2hahn import T2Hahn

delays = list(range(0, 50, 1))
delays = [float(_)*conversion_factor for _ in delays]
number_of_echoes = 1

exp = T2Hahn(physical_qubits=(0, ), delays=delays, num_echoes=number_of_echoes, backend=backend)
print(exp.circuits()[0])

# mwc
exp.analysis.set_options(p0=None, plot=True)
exp.set_run_options(shots=1000, seed_simulator=121)
expdata = exp.run().block_for_results()

display(expdata.figure(0))
```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/eac0eaec-9419-4f27-bc25-0f0b318c260b)





### T"*-Ramsey :

```
# backend
from qiskit.providers.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakeManilaV2(), 
                                      thermal_relaxation=True, 
                                      gate_error=False, 
                                      readout_error=False,                                           
)

backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

# mwc
import numpy as np
import qiskit
from qiskit_experiments.library import T2Ramsey

delays = list(np.arange(1.00e-6, 50.0e-6, 2.00e-6))
exp = T2Ramsey(physical_qubits=(0, ), delays=delays, osc_freq=1.0e5, backend=backend)
print(exp.circuits()[0])

# mwc
exp.set_transpile_options(scheduling_method="asap")
exp.set_run_options(shots=1000, seed_simulator=101)
expdata = exp.run().block_for_results()

display(expdata.figure(0))
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/6a25e823-76b5-4031-8a72-4c253bca94f5)







### Tphi-experiment

```
# backend
from qiskit.providers.fake_provider import FakeManilaV2
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakeManilaV2(), 
                                      thermal_relaxation=True, 
                                      gate_error=False, 
                                      readout_error=False, 
                                     )

backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

# mwc
import numpy as np
import qiskit
from qiskit_experiments.library.characterization import Tphi

delays_t1 = np.arange(1e-6, 300e-6, 10e-6)
delays_t2 = np.arange(1e-6, 50e-6, 2e-6)

# mwc
exp = Tphi(physical_qubits=(0, ), delays_t1=delays_t1, delays_t2=delays_t2, num_echoes=1, backend=backend)
exp.set_run_options(seed_simulator=100)
exp_data = exp.run().block_for_results()
result = exp_data.analysis_results("T_phi")
print(result)
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
# backend
from qiskit import Aer
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakeSydneyV2

backend = AerSimulator.from_backend(FakeSydneyV2())

# mwc
from qiskit_experiments.framework import BatchExperiment
from qiskit_experiments.library import QuantumVolume

qubits = tuple(range(4)) # Can use specific qubits. for example [2, 4, 7, 10]
qv_exp = QuantumVolume(qubits, seed=42)
# Transpile options like optimization_level affect only the real device run and not the simulation run
# Run options affect both simulation and real device runs
qv_exp.set_transpile_options(optimization_level=3)

expdata = qv_exp.run(backend).block_for_results()

display(expdata.figure(0))

for result in expdata.analysis_results():
    print(result)
```



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/6ad78dee-4fa8-47ab-8e8f-cae1c99de1db)





AnalysisResult
- name: mean_HOP
- value: 0.74+/-0.04
- quality: bad
- extra: <4 items>
- device_components: ['Q0', 'Q1', 'Q2', 'Q3']
- verified: False
AnalysisResult
- name: quantum_volume
- value: 1
- quality: bad
- extra: <4 items>
- device_components: ['Q0', 'Q1', 'Q2', 'Q3']
- verified: False





### QS
```
# backend
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=199)

# mwc
import numpy as np
from qiskit_experiments.library.characterization import QubitSpectroscopy

qubit = 0
freq01_estimate = backend.defaults().qubit_freq_est[qubit]
frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)

exp = QubitSpectroscopy(physical_qubits = (0,),                        
                        frequencies = frequencies, 
                        backend = backend, 
                        absolute = True,
                        )
exp.set_experiment_options(amp=0.005)  # This is not a default value

# mwc
exp_data = exp.run().block_for_results()
exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/c176aac9-3570-4b00-853f-085028c02398)







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
    pulse.play(
        pulse.Gaussian(160, Parameter("amp"), sigma=40),
        pulse.DriveChannel(0)
    )

exp = Rabi(
    physical_qubits=(0,),
    backend=backend,
    schedule=build_sched,
    amplitudes=np.linspace(-0.1, 0.1, 21),
)

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
from qiskit_experiments.library import RoughDrag
from qiskit.circuit import Parameter
from qiskit import pulse

beta = Parameter("β")
with pulse.build() as build_sched:
    pulse.play(pulse.Drag(160, 0.50, 40, beta), pulse.DriveChannel(0))

exp = RoughDrag(physical_qubits=(0,), 
                schedule=build_sched,
                betas = np.linspace(-4, 4, 51), 
                backend=backend)
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
from qiskit.pulse import InstructionScheduleMap
from qiskit_experiments.library import FineXAmplitude

qubit = 0
x_pulse = backend.defaults().instruction_schedule_map.get('x', (qubit,)).instructions[0][1].pulse
drive_chan = pulse.DriveChannel(qubit)
inst_map = pulse.InstructionScheduleMap()

ideal_amp = x_pulse.amp
over_amp = ideal_amp*1.01
with pulse.build(backend=backend, name="x") as x_over:
    pulse.play(pulse.Drag(x_pulse.duration, over_amp, x_pulse.sigma, x_pulse.beta), drive_chan)
inst_map.add("x", (qubit,), x_over) 

exp = FineXAmplitude(qubit, backend=backend)
exp.set_transpile_options(inst_map=inst_map)
exp_data = exp.run().block_for_results()
exp_data.figure(0)
```

 



![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2b007bcf-f5b9-4fd7-9df9-3db701428c94)







### LocalReadoutError
```
# backend 
from qiskit_aer import AerSimulator
from qiskit.providers.fake_provider import FakePerth

backend = AerSimulator.from_backend(FakePerth())

# mwc
from qiskit_experiments.library import LocalReadoutError

qubits = list(range(4))
shots=10000

exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
exp.analysis.set_options(plot=True)
exp.set_run_options(shots=shots)

result = exp.run()
mitigator = result.analysis_results(0).value

result.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/88e75bfd-6eab-4f18-b6b4-dd67df46036b)






### CorrelatedReadoutError
```
# backend & noise_model
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel
from qiskit.providers.fake_provider import FakePerth

noise_model = NoiseModel.from_backend(FakePerth(), 
                                      thermal_relaxation = False, 
                                      gate_error = False, 
                                      readout_error = True, 
                                     )

backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

# mwc
from qiskit_experiments.library import CorrelatedReadoutError

qubits = [0,1,2]
exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)

for circ in exp.circuits():
    print(circ)

shots=1000
exp.set_run_options(shots=shots)
exp.analysis.set_options(plot=True)
result = exp.run()
mitigator = result.analysis_results(0).value

result.figure(0)
```

Note that the noise_model defined from a FakeBackend does not support correlated-reoudout-error.





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/da36777c-9b78-4cc8-a1f0-4f458a64021f)






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







