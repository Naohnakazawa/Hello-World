# Characterization experiments 16, 17, 18, 19, 20, 21, 22, 23

### Fine Frequency
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend
from qiskit_experiments.test.mock_iq_helpers import MockIQFineFreqHelper as FineFreqHelper


sx_duration=160
freq_shift_list=[-0.5e-6, -0.1e-6, 0.1e-6, 0.5e-6]
freq_shift=freq_shift_list[0]

exp_helper=FineFreqHelper(sx_duration, freq_shift)
backend=MockIQBackend(exp_helper)
exp_helper_dt=BackendData(backend).dt

# ..jupyter-execute::

# mwc
import numpy as np
from qiskit import pulse
from qiskit.pulse import InstructionScheduleMap, DriveChannel
from qiskit_experiments.library import FineFrequency

qubit=0
inst_map = InstructionScheduleMap()
with pulse.build(name="sx") as sched_build:
    pulse.play(pulse.Gaussian(duration=sx_duration, amp=0.5, sigma=40), pulse.DriveChannel(0))
inst_map.add("sx", qubit, sched_build)

exp = FineFrequency([qubit], sx_duration, backend)
exp.set_transpile_options(inst_map=inst_map)
exp.set_run_options(shots=1000, seed_simulator=175)
exp_data = exp.run().block_for_results()

d_theta = exp_data.analysis_results(1).value.n
print(d_theta)
d_freq = d_theta / (2*np.pi*sx_duration*exp_helper_dt)
print(d_freq)

exp_data.figure(0)
```

1.7040192705815202e-05

12.712962669546187





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/96ebdd1b-8ca3-4424-a0ef-8a353affa215)






### RamseyXY
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend
from qiskit_experiments.test.mock_iq_helpers import MockIQRamseyXYHelper

exp_helper=MockIQRamseyXYHelper(t2ramsey=5.e-6, 
                               freq_shift=0., )
backend=MockIQBackend(exp_helper)
exp_helper_dt=BackendData(backend).dt

# ..jupyter-execute::

# mwc
import numpy as np
from qiskit_experiments.library.characterization import RamseyXY

qubit=0
delays = np.linspace(0, 10.e-6, 51)
exp = RamseyXY([qubit], backend=backend, delays=delays, osc_freq=0.01e6)
exp.set_run_options(shots=1000, seed_simulator=201)
exp_data = exp.run().block_for_results()
exp_data.figure(0)

```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/13cf758b-233d-4949-9cee-b53d4e83d7b2)






### ZZRamsey
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend
from qiskit.providers.fake_provider import FakePerth
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel

noise_model = NoiseModel.from_backend(FakePerth(), 
                                      thermal_relaxation=True, 
                                      gate_error=False, 
                                      readout_error=False,                                           
)

backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

# ..jupyter-execute::

#mwc
from qiskit_experiments.library.characterization import ZZRamsey

qubits = (0, 1)
exp = ZZRamsey(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=1000, seed_simulator=101)
print(exp.circuits()[2])
print(exp.circuits()[3])

exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()
print(f"The value of {exp_data.analysis_results(1).name} is {exp_data.analysis_results(1).value}")

exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/1b39765b-373d-4c50-868a-2d4e0989584d)








### ResonatorSpectroscopy
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm-nairobi
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_nairobi")

initial_layout = list(range(7))

# ..jupyter-execute::

# mwc 
import numpy as np
from qiskit_experiments.library.characterization import ResonatorSpectroscopy

qubit = 0
exp = ResonatorSpectroscopy(physical_qubits = [qubit], backend = backend)
exp.set_run_options(shots=1000, seed_simulator=119)

exp_data = exp.run().block_for_results()
result = exp_data.analysis_results()
exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/e8a47195-03dd-421f-bf99-eff7cbbe9f4b)






### MultiStateDiscrimination
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import MultiStateDiscrimination

qubit=0
exp=MultiStateDiscrimination([qubit], backend=backend)
exp_data=exp.run().block_for_results()
result=exp_data.analysis_results()
exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/9efc11be-ad8f-450f-a001-76f5d42b286a)






### CRH
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

initial_layout = list(range(127))

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import CrossResonanceHamiltonian

qubits=(17, 30)
exp=CrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)
exp.set_experiment_options(max_circuits=None, 
                       flat_top_widths=None, 
                       durations=None, 
                       min_durations=1.0e-07, 
                       max_durations=1.2e-06, 
                       num_durations=48, 
                       amp=0.2024212826036257, 
                       amp_t=0.0, 
                       sigma=30, 
                       risefall=2)

exp.circuits()[287].draw("mpl")

# run carefully on real devices!
# exp_data=exp.run()
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/35f11f7c-9fe4-46d0-9a64-c4126e0485e5)





### ECRH
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

initial_layout = list(range(127))

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import EchoedCrossResonanceHamiltonian

qubits=(17, 30)
exp=EchoedCrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)

exp.set_experiment_options(max_circuits=None, 
                       flat_top_widths=None, 
                       durations=None, 
                       min_durations=1.0e-07, 
                       max_durations=1.2e-06, 
                       num_durations=48, 
                       amp=0.2024212826036257, 
                       amp_t=0.0, 
                       sigma=30, 
                       risefall=2)

exp.circuits()[287].draw("mpl")

# run carefully on real devices!
# exp_data=exp.run()
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a547ff5d-481b-4cd0-8aa3-1d3565ae031d)






### StarkP1Spectroscopy
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")
initial_layout = list(range(127))

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization.t1 import StarkP1Spectroscopy

qubit=13
exp = StarkP1Spectroscopy((qubit,), backend=backend)
exp.set_run_options(shots=100)
exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/752fc54e-5244-4ac9-927b-a3ba9dc72c09)






