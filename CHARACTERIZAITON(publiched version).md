# Characterization experiments

### CorrelatedReadoutError (revised)
```
# section: example
# ..jupyter-execute::
# :hide-code:

from qiskit_ibm_runtime.fake_provider import FakePerth
from qiskit_aer import AerSimulator

backend = AerSimulator.from_backend(FakePerth())

# ..jupyter-execute::

from qiskit_experiments.library import CorrelatedReadoutError
            
exp = CorrelatedReadoutError(physical_qubits=(0,1,2), backend=backend)
exp.analysis.set_options(plot=True)

exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/f7003eb7-4597-4eb4-b503-19719213feb3)




### CrossResonanceHamiltonian
```
```

### EchoedCRH
```
```

### RoughDrag
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=101)

        ..jupyter-execute::

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

### EFSpectroscopy(published version must be replaced with the revised version 20240629)
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

        ..jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import EFSpectroscopy

            qubit = 0
            freq01_estimate = backend.defaults().qubit_freq_est[qubit]
            freq12_estimate = freq_01 + (-.25e9)
            frequencies = np.linspace(freq12_estimate-15e6, freq12_estimate+15e6, 51)

            exp = EFSpectroscopy(physical_qubits = (0,),
                                     frequencies = frequencies,
                                     backend = backend,
                                     absolute = True,
                                    )
            exp.set_experiment_options(amp=0.005)

            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/78d0dace-f5ed-4dad-854b-37f6604cbaa4)







### FineAmplitude(XGate)
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e6, noise=True, seed=185)

        ..jupyter-execute::

            import numpy as np
            from qiskit.circuit.library import XGate
            from qiskit_experiments.library import FineAmplitude
            
            qubit = 0
            exp = FineAmplitude(physical_qubits=(qubit,), gate=XGate(), backend=backend)
            exp.set_run_options(shots=10000)
            exp.analysis.set_options(fixed_parameters={"angle_per_gate" : np.pi, "phase_offset" : np.pi})            
            print(exp.circuits()[5])

            exp_data = exp.run().block_for_results()
            result =exp_data.analysis_results()               
            exp_data.figure(0)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2617a69c-1af3-41f2-96b2-58b0b787211f)







### FineXAmplitude
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=198)

        ..jupyter-execute::

            from qiskit_experiments.library import FineXAmplitude

            qubit = 0
            exp = FineXAmplitude(qubit, backend=backend)
            exp.circuits()[5].draw("mpl")

        ..jupyter-execute::

            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```

### FineSXAmplitude
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=198)

        ..jupyter-execute::

            from qiskit_experiments.library import FineSXAmplitude
  
            qubit = 0
            exp = FineSXAmplitude(physical_qubits=(qubit,), backend=backend)            
            exp.circuits()[5].draw("mpl")

        ..jupyter-execute::

            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```


### FineDrag
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        ..jupyter-execute::

            from qiskit.circuit.library import XGate
            from qiskit_experiments.library.characterization import FineDrag

            qubit=0
            exp = FineDrag(physical_qubits=(qubit,), gate=XGate(), backend=backend)
            exp.set_run_options(shots=1000, seed_simulator=101)

            exp_data = exp.run().block_for_results()
            result = exp_data.analysis_results()
            exp_data.figure(0)
```

### FineXDrg
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        ..jupyter-execute::

            from qiskit_experiments.library.characterization import FineXDrag

            qubit=0
            exp = FineXDrag(physical_qubits=(qubit,), backend=backend)
            exp.set_run_options(shots=1000, seed_simulator=101)

            exp_data = exp.run().block_for_results()
            result = exp_data.analysis_results()
            exp_data.figure(0)
```

### FineSXDrag
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        ..jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import FineSXDrag

            qubit=0
            exp = FineSXDrag(physical_qubits=(qubit,), backend=backend)
            exp.analysis.set_options(normalization= True,              # 0319
                                     fixed_parameters={                #
                                         "angle_per_gate" : 0.0,       #
                                         "phase_offset" : np.pi/2,     #
                                         "amp" : 0.6                   #
                                         },                            #
                                    )                                  #
            print(exp.circuits()[1])

        ..jupyter-execute::

            exp.set_run_options(shots=10000, seed_simulator=101)
            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/79c856d6-d0f9-4631-ad34-caca31a44943)





### FineFrequency (revised backned = Aer(FakePerth)
```
    # section: example
        .. jupyter-execute::
            # :hide-code:

            # backend
            from qiskit_ibm_runtime.fake_provider FakePerth
            from qiskit_aer import AerSimulator
            backend =AerSimulaor.from_backend(FakePerth())

```

### HalfAngle
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        ..jupyter-execute::

            from qiskit_experiments.library.characterization import HalfAngle

            qubit=0
            exp = HalfAngle([qubit], backend=backend)
            exp.set_run_options(shots=1000, seed_simulator=178)

            exp_data = exp.run().block_for_results()
            result = exp_data.analysys_results()
            exp_data.figure(0)
```

### LocalReadoutError
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend 
            from qiskit_aer import AerSimulator
            # from qiskit.providers.fake_provider import FakePerth
            from qiskit_ibm_runtime.fake_provider import FakePerth # for qiskit v1.0.0
            backend = AerSimulator.from_backend(FakePerth())

        ..jupyter-execute::

            from qiskit_experiments.library import LocalReadoutError

            qubits = list(range(4))
            exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
            exp.analysis.set_options(plot=True)
            exp.set_run_options(shots=10000)

            result = exp.run()
            mitigator = result.analysis_results(0).value

            result.figure(0)
```

### MultiStateDiscrimination
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend 
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        ..jupyter-execute::

            from qiskit_experiments.library.characterization import MultiStateDiscrimination

            qubit=0
            exp=MultiStateDiscrimination([qubit], backend=backend)
            print(exp.circuits()[0])
            print(exp.circuits()[1])

            exp_data=exp.run().block_for_results()
            result=exp_data.analysis_results()
            exp_data.figure(0)
```

### QibitSpectroscopy
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=199)

        ..jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import QubitSpectroscopy

            qubit = 0
            freq01_estimate = backend.defaults().qubit_freq_est[qubit]
            frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)

            exp = QubitSpectroscopy(physical_qubits = (0,), 
                                    frequencies = frequencies, 
                                    backend = backend, 
                                    absolute = True,
                                    analysis = None,
                                    )
            exp.set_experiment_options(amp=0.005)

        ..jupyter-execute::

            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```

### Rabi
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend 
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9, seed=200)

        ..jupyter-execute::

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

### EFRabi (published version must be improved)
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend 
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9, seed=200)

        ..jupyter-execute::

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

### EFRabi(revised 20240705 ???)
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




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/084ee3a7-b80a-40af-9954-92ca3d2f93d7)







### RamseyXY
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend 
            from qiskit_aer import AerSimulator
            # from qiskit.providers.fake_provider import FakePerth
            from qiskit_ibm_runtime.fake_provider import FakePerth # for qiskit v1.0.0
            backend = AerSimulator.from_backend(FakePerth())

        ..jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import RamseyXY

            qubit=0
            delays = np.linspace(0, 10.e-7, 101)
            exp = RamseyXY([qubit], backend=backend, delays=delays, osc_freq=2.0e6)
            exp.set_run_options(shots=1000, seed_simulator=201)

            exp_data = exp.run().block_for_results()
            result=exp_data.analysis_results()
            exp_data.figure(0)
```

### StarkRamseyXY
```
```

### StarkRamseyXYAmpScan
```
```

### ReadoutAngle
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend  
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        ..jupyter-execute::

            from qiskit_experiments.library.characterization import ReadoutAngle
            qubit=0
            exp = ReadoutAngle([qubit], backend=backend)
            exp.set_run_options(shots=1000, seed_simulator=106)

            exp_data = exp.run().block_for_results()
            result = exp_data.analysis_results()
            for _ in result:
                print(_)

        ..jupyter-execute::

            exp_data.figure(0)
```

### ResonatorSpectroscopy
```
```

### T1
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend 
            # from qiskit.providers.fake_provider import FakeManilaV2
            from qiskit_ibm_runtime.fake_provider import FakeManilaV2 # for qiskit v1.0.0
            from qiskit_aer import AerSimulator
            backend = AerSimulator.from_backend(FakeManilaV2())

        ..jupyter-execute::

            from qiskit_experiments.library import T1
            import numpy as np

            delays = np.arange(1.e-6, 300.e-6, 30.e-6)
            exp = T1(physical_qubits=(0, ), delays=delays, backend=backend)
            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
```

### StarkP1Spectroscopy
```
```

### T2Hahn (revised)
```
    # section: example
        ..jupyter-execute::
            :hide-code:

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

        ..jupyter-execute::

            import qiskit
            from qiskit_experiments.library.characterization.t2hahn import T2Hahn

            conversion_factor = 1e-6
            delays = list(range(0, 50, 1))
            delays = [float(_)*conversion_factor for _ in delays]
            number_of_echoes = 1

            exp = T2Hahn(physical_qubits=(0, ), delays=delays, num_echoes=number_of_echoes, backend=backend)
            print(exp.circuits()[0])

         ..jupyter-execute::

            exp.analysis.set_options(p0=None, plot=True)
            exp.set_run_options(shots=1000, seed_simulator=121)
            expdata = exp.run().block_for_results()

            display(expdata.figure(0))
```

### T2Ramsey
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            # from qiskit.providers.fake_provider import FakeManilaV2
            from qiskit_ibm_runtime.fake_provider import FakeManilaV2 # for qiskit v1.0.0
            from qiskit_aer import AerSimulator
            from qiskit_aer.noise import NoiseModel
            noise_model = NoiseModel.from_backend(FakeManilaV2(), 
                                                  thermal_relaxation=True, 
                                                  gate_error=False, 
                                                  readout_error=False,                                           
                                                  )

            backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

        ..jupyter-execute::

            import numpy as np
            import qiskit
            from qiskit_experiments.library import T2Ramsey

            delays = list(np.arange(1.00e-6, 50.0e-6, 2.00e-6))
            exp = T2Ramsey(physical_qubits=(0, ), delays=delays, backend=backend, osc_freq=1.0e5)
            print(exp.circuits()[0])

        ..jupyter-execute::

            exp.set_run_options(shots=1000, seed_simulator=101)
            expdata = exp.run().block_for_results()

            display(expdata.figure(0))
```

### Tphi
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            # from qiskit.providers.fake_provider import FakeManilaV2
            from qiskit_ibm_runtime.fake_provider import FakeManilaV2 # for qiskit v1.0.0
            from qiskit_aer import AerSimulator
            from qiskit_aer.noise import NoiseModel

            noise_model = NoiseModel.from_backend(FakeManilaV2(), 
                                                  thermal_relaxation=True, 
                                                  gate_error=False, 
                                                  readout_error=False, 
                                                 )

            backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

        ..jupyter-execute::

            import numpy as np
            import qiskit
            from qiskit_experiments.library.characterization import Tphi

            delays_t1 = np.arange(1e-6, 300e-6, 10e-6)
            delays_t2 = np.arange(1e-6, 50e-6, 2e-6)

        ..jupyter-execute::

            exp = Tphi(physical_qubits=(0, ),
                        delays_t1=delays_t1,
                        delays_t2=delays_t2,
                        t2type="hahn",
                        osc_freq = 5.e-6, 
                        num_echoes=1,
                        backend=backend
                        )
            exp.set_run_options(seed_simulator=100)
            exp_data = exp.run().block_for_results()

            result = exp_data.analysis_results("T_phi")
            print(result)
```

### ZZRamsey
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend
            # from qiskit.providers.fake_provider import FakePerth
            from qiskit_ibm_runtime.fake_provider import FakePerth # for qiskit v1.0.0
            from qiskit_aer import AerSimulator
            from qiskit_aer.noise import NoiseModel

            noise_model = NoiseModel.from_backend(FakePerth(), 
                                                  thermal_relaxation=True, 
                                                  gate_error=False, 
                                                  readout_error=False,                                           
            )

            backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

        ..jupyter-execute::

            from qiskit_experiments.library.characterization import ZZRamsey

            qubits = (0, 1)
            exp = ZZRamsey(physical_qubits=qubits, backend=backend)
            exp.set_run_options(shots=1000, seed_simulator=101)

            exp_data = exp.run().block_for_results()
            result=exp_data.analysis_results()
#            print(f"The value of {exp_data.analysis_results(1).name} is {exp_data.analysis_results(1).value}")

            exp_data.figure(0)
```

