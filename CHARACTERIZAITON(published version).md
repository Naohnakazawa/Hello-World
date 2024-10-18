# Characterization experiments

### CorrelatedReadoutError (revised)
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            from qiskit.providers.fake_provider import GenericBackendV2
            from qiskit_aer import AerSimulator

            num_qubits=5
            backend = AerSimulator.from_backend(GenericBackendV2(num_qubits=num_qubits,
                                                                 calibrate_instructions=True))

        .. jupyter-execute::

            from qiskit_experiments.library import CorrelatedReadoutError

            exp = CorrelatedReadoutError(physical_qubits=(0,1,2), backend=backend)

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
            exp_data.analysis_results(dataframe=True)
```

### EFSpectroscopy(published version must be replaced with this revised version 20240629)
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

            freq01_estimate = backend.defaults().qubit_freq_est[0]
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
            exp_data.analysis_results(dataframe=True)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/78d0dace-f5ed-4dad-854b-37f6604cbaa4)







### FineAmplitude(XGate)
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e6, noise=True, seed=185)

        .. jupyter-execute::

            import numpy as np
            from qiskit.circuit.library import XGate
            from qiskit_experiments.library import FineAmplitude

            exp = FineAmplitude(physical_qubits=(0,), gate=XGate(), backend=backend)
            exp.analysis.set_options(fixed_parameters={"angle_per_gate" : np.pi, "phase_offset" : np.pi})

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/2617a69c-1af3-41f2-96b2-58b0b787211f)







### FineXAmplitude
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=198)

        .. jupyter-execute::

            from qiskit_experiments.library import FineXAmplitude

            exp = FineXAmplitude(physical_qubits=(0,), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### FineSXAmplitude
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=198)

        .. jupyter-execute::

            from qiskit_experiments.library import FineSXAmplitude

            exp = FineSXAmplitude(physical_qubits=(0,), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```


### FineDrag
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        .. jupyter-execute::

            from qiskit.circuit.library import XGate
            from qiskit_experiments.library.characterization import FineDrag

            exp = FineDrag(physical_qubits=(0,), gate=XGate(), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### FineXDrg
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        .. jupyter-execute::

            from qiskit_experiments.library.characterization import FineXDrag

            exp = FineXDrag(physical_qubits=(0,), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### FineSXDrag
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        .. jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import FineSXDrag

            exp = FineSXDrag(physical_qubits=(0,), backend=backend)
            exp.analysis.set_options(normalization= True,
                                     fixed_parameters={
                                         "angle_per_gate" : 0.0,
                                         "phase_offset" : np.pi/2,
                                         "amp" : 0.6
                                         },
                                    )

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/79c856d6-d0f9-4631-ad34-caca31a44943)







### FineFrequency (revised backned = Aer(FakePerth)
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_ibm_runtime.fake_provider import FakePerth
            from qiskit_aer import AerSimulator
            backend =AerSimulator.from_backend(FakePerth())

        .. jupyter-execute::

            from qiskit_experiments.library.characterization import FineFrequency

            repetitions = list(range(40))

            exp = FineFrequency((0,),
                                delay_duration=320,
                                backend=backend,
                                repetitions=repetitions)
            exp.set_transpile_options(optimization_level=0, basis_gates=['sx', 'rz', 'delay'])

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### HalfAngle
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        .. jupyter-execute::

            from qiskit_experiments.library.characterization import HalfAngle

            exp = HalfAngle((0,), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### LocalReadoutError
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_aer import AerSimulator
            from qiskit_ibm_runtime.fake_provider import FakePerth
            backend = AerSimulator.from_backend(FakePerth())

        .. jupyter-execute::

            from qiskit_experiments.library import LocalReadoutError

            qubits = list(range(4))
            exp = LocalReadoutError(physical_qubits=qubits, backend=backend)
            exp.analysis.set_options(plot=True)
            exp.set_run_options(shots=10000)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### MultiStateDiscrimination
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        .. jupyter-execute::

            from qiskit_experiments.library.characterization import MultiStateDiscrimination

            exp=MultiStateDiscrimination((0,), backend=backend)

            exp_data=exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### QibitSpectroscopy
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=199)

        .. jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import QubitSpectroscopy

            qubit = 0
            freq01_estimate = backend.defaults().qubit_freq_est[qubit]
            frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)

            exp = QubitSpectroscopy(physical_qubits = (qubit,),
                                    frequencies = frequencies,
                                    backend = backend,
                                    )
            exp.set_experiment_options(amp=0.005)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### Rabi
```
   # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-0.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=199)

        .. jupyter-execute::

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
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### EFRabi (This published version must be improved)
```
   # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-0.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=199)

        .. jupyter-execute::

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
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### EFRabi(The publised version must be replace with this revised 20240705)
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
        pulse.shift_frequency(-0.25e9, pulse.DriveChannel(0)) # frequency shift is necessary!
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
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_aer import AerSimulator
            from qiskit_ibm_runtime.fake_provider import FakePerth

            backend = AerSimulator.from_backend(FakePerth())

        .. jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization import RamseyXY

            delays = np.linspace(0, 10.e-7, 101)
            exp = RamseyXY((0,), backend=backend, delays=delays, osc_freq=2.0e6)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
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
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
            backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

        .. jupyter-execute::

            from qiskit_experiments.library.characterization import ReadoutAngle

            exp = ReadoutAngle((0,), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### ResonatorSpectroscopy
```
```

### T1
```
    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_ibm_runtime.fake_provider import FakeManilaV2
            from qiskit_aer import AerSimulator
            backend = AerSimulator.from_backend(FakeManilaV2())

        .. jupyter-execute::

            import numpy as np
            from qiskit_experiments.library import T1

            delays = np.arange(1.e-6, 300.e-6, 30.e-6)
            exp = T1(physical_qubits=(0, ), delays=delays, backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### StarkP1Spectroscopy
```
```

### T2Hahn (revised)
```
    # section: example
        .. jupyter-execute::
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

        .. jupyter-execute::

            import numpy as np
            from qiskit_experiments.library.characterization.t2hahn import T2Hahn

            delays = np.linspace(0, 50, 51)*1e-6

            exp = T2Hahn(physical_qubits=(0, ),
                         delays=delays,
                         backend=backend)
            exp.analysis.set_options(p0=None, plot=True)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### T2Ramsey
```
  # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_ibm_runtime.fake_provider import FakeManilaV2
            from qiskit_aer import AerSimulator
            from qiskit_aer.noise import NoiseModel

            noise_model = NoiseModel.from_backend(FakeManilaV2(),
                                                  thermal_relaxation=True,
                                                  gate_error=False,
                                                  readout_error=False,
                                                  )

            backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

        .. jupyter-execute::

            import numpy as np
            import qiskit
            from qiskit_experiments.library import T2Ramsey

            delays = list(np.arange(1.00e-6, 50.0e-6, 2.00e-6))
            exp = T2Ramsey(physical_qubits=(0, ), delays=delays, backend=backend, osc_freq=1.0e5)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

### Tphi
```
  # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_ibm_runtime.fake_provider import FakeManilaV2
            from qiskit_aer import AerSimulator
            from qiskit_aer.noise import NoiseModel

            noise_model = NoiseModel.from_backend(FakeManilaV2(),
                                                  thermal_relaxation=True,
                                                  gate_error=False,
                                                  readout_error=False,
                                                 )

            backend = AerSimulator.from_backend(FakeManilaV2(), noise_model=noise_model)

        .. jupyter-execute::

            import numpy as np
            import qiskit
            from qiskit_experiments.library.characterization import Tphi

            delays_t1 = np.arange(1e-6, 300e-6, 10e-6)
            delays_t2 = np.arange(1e-6, 50e-6, 2e-6)

            exp = Tphi(physical_qubits=(0, ),
                        delays_t1=delays_t1,
                        delays_t2=delays_t2,
                        backend=backend
                        )

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            display(exp_data.figure(1))
            exp_data.analysis_results(dataframe=True)
```

### ZZRamsey
```

    # section: example
        .. jupyter-execute::
            :hide-code:

            # backend
            from qiskit_ibm_runtime.fake_provider import FakePerth
            from qiskit_aer import AerSimulator
            from qiskit_aer.noise import NoiseModel

            noise_model = NoiseModel.from_backend(FakePerth(),
                                                  thermal_relaxation=True,
                                                  gate_error=False,
                                                  readout_error=False,
            )

            backend = AerSimulator.from_backend(FakePerth(), noise_model=noise_model)

        .. jupyter-execute::

            from qiskit_experiments.library.characterization import ZZRamsey

            exp = ZZRamsey(physical_qubits=(0,1), backend=backend)

            exp_data = exp.run().block_for_results()
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
```

