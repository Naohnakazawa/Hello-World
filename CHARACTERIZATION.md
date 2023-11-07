# Characterization experimnents

### CorrelatedReadoutError
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

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

    # ..jupyter-execute::

        # mwc
        from qiskit_experiments.library import CorrelatedReadoutError

        qubits = [0,1,2]
        exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)

        for circ in exp.circuits():
            print(circ)

    # ..jupyter-execute::

        shots=1000
        exp.set_run_options(shots=shots)
        exp.analysis.set_options(plot=True)
        result = exp.run()
        mitigator = result.analysis_results(0).value

        result.figure(0)
```

### RoughDrag
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend 
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=101)

    # ..jupyter-execute::

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

### EFSpectroscopy
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, noise=False, seed=100)

    # ..jupyter-execute::

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

### FineAmplitude
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=198)

    # ..jupyter-execute::

        from qiskit_experiments.library import FineXAmplitude

        qubit = 0
        exp = FineXAmplitude(qubit, backend=backend)
        exp.circuits()[5].draw("mpl")

    # ..jupyter-execute::

        exp_data = exp.run().block_for_results()
        exp_data.figure(0)
```

### FineDrag
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend 
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199) # "noise" option is "False"

    # ..jupyter-execute::

        from qiskit_experiments.library.characterization import FineXDrag

        qubit=0
        exp = FineXDrag([qubit], backend=backend)
        print(exp.circuits()[3])

    # ..jupyter-execute::

        exp.set_run_options(shots=1000, seed_simulator=101)
        exp_data = exp.run().block_for_results()
        exp_data.figure(0)

```

### FineFrequency
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

### HalfAngle
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend 
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199)

    # ..jupyter-execute::

        from qiskit import pulse
        from qiskit_experiments.library.characterization import HalfAngle

        qubit=0
        exp = HalfAngle([qubit], backend=backend)
        print(exp.circuits()[5])

    # ..jupyter-execute::

        inst_map = backend.defaults().instruction_schedule_map
        with pulse.build(backend=backend, name="y") as sched_build:
            pulse.play(pulse.Drag(duration=160, 
                                  sigma=40, 
                                  beta=5, 
                                  amp=0.05821399464431249, 
                                  angle=0.0, 
                                  name="Yp_d0"
                                 ), pulse.DriveChannel(0), name="Yp_d0")
        inst_map.add("y", (qubit,), sched_build)    
        exp.set_transpile_options(inst_map=inst_map)
        exp.set_run_options(shots=1000, seed_simulator=178)

        exp_data = exp.run().block_for_results()
        exp_data.figure(0)
```

### LocalReadoutError
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend 
        from qiskit_aer import AerSimulator
        from qiskit.providers.fake_provider import FakePerth

        backend = AerSimulator.from_backend(FakePerth())

    # ..jupyter-execute::

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

### MultiStateDiscrimination
```


```

### QibitSpectroscopy
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=True, seed=199)

    # ..jupyter-execute::

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
        exp.set_experiment_options(amp=0.005)  # This is not a default value

    # ..jupyter-execute::

        exp_data = exp.run().block_for_results()
        exp_data.figure(0)
```

### Rabi
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend 
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9, seed=200)

    # ..jupyter-execute::

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

### EFRabi
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend 
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9, seed=200)

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

        exp_data = exp.run().block_for_results()
        exp_data.figure(0)
```

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

        import numpy as np
        from qiskit_experiments.library.characterization import RamseyXY

        qubit=0
        delays = np.linspace(0, 10.e-6, 51)
        exp = RamseyXY([qubit], backend=backend, delays=delays, osc_freq=0.01e6)
        exp.set_run_options(shots=1000, seed_simulator=201)
        exp_data = exp.run().block_for_results()
        exp_data.figure(0)
```

### ReadoutAngle
```
# section: example
    # ..jupyter-execute::
        # :hide-code:

        # backend  
        from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
        backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=199) # "noise" option is "False"

    # ..jupyter-execute::

        from qiskit_experiments.library.characterization import ReadoutAngle
        qubit=0
        exp = ReadoutAngle([qubit], backend=backend)
        exp.set_run_options(shots=1000, seed_simulator=106)

        exp_data = exp.run().block_for_results()
        result = exp_data.analysis_results()
        for _ in result:
            print(_)

    # ..jupyter-execute::

        exp_data.figure(0)
```

### ResonatorSpectroscopy
```



```

### T1
```

```

### T2Hahn
```


```

### T2Ramsey
```


```

### Tphi
```


```

### ZZRamsey
```


```



