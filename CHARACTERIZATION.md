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
