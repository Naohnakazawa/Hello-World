# Characterization experiments 1,2,3,4,5


### Qubit Spectroscopy
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

delays = np.arange(1.e-6, 300.e-6, 30.e-6)
exp = T1(physical_qubits=(0, ), delays=delays, backend=backend)
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
exp = T2Ramsey(physical_qubits=(0, ), delays=delays, backend=backend, osc_freq=1.0e5)
print(exp.circuits()[0])

# mwc
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


