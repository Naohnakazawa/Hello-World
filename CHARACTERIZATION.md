# Characterization experimnents

### CorrelatedReadoutError
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend=ibm_brisbane
            from qiskit_ibm_runtime import QiskitRuntimeService
            service = QiskitRuntimeService(channel="ibm_quantum") 
            backend = service.backend("ibm_brisbane")

        ..jupyter-execute::

            # In this example, the backend is assumed to be a real device
            # on the IBM Quantum platform

            from qiskit_experiments.library import CorrelatedReadoutError
  
            qubits = (120,119,)
            exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)
            exp.set_run_options(shots=1000)
            exp.analysis.set_options(plot=True)

            step1=False # "False" shows the result saved in a previous job
            if step1==True:

                exp_data = exp.run().block_for_results()
                mitigator = exp_data.analysis_results(0).value

            else:
                # retrieve your jobs
                from qiskit_ibm_provider import IBMProvider
                from qiskit_experiments.framework import ExperimentData
    
                provider=IBMProvider()
                job_ids= ["cqs655w4x0mg008jgccg"]
    
                exp_data = ExperimentData(experiment=exp)
                exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
                exp.analysis.run(exp_data)
                exp_data.block_for_results()

            result=exp_data.analysis_results()
            exp_data.figure(0)
```

### CorrelatedReadoutError (AerSimulator)
```

    # section: example
        ..jupyter-execute::
            :hide-code:

            from qiskit.providers.fake_provider import GenericBackendV2
            from qiskit_aer import AerSimulator
           
            num_qubits=5
            backend = AerSimulator.from_backend(GenericBackendV2(num_qubits=num_qubits, calibrate_instructions=True))
            
        ..jupyter-execute::

            from qiskit_experiments.library import CorrelatedReadoutError
            
            qubits = [0,1,2]
            exp = CorrelatedReadoutError(physical_qubits=qubits, backend=backend)
            exp.analysis.set_options(plot=True)            
            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```

### CrossResonanceHamiltonian
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend ibm_brisbane
            from qiskit_ibm_runtime import QiskitRuntimeService
            service = QiskitRuntimeService(channel="ibm_quantum")
            backend = service.backend("ibm_brisbane")

        ..jupyter-execute::

            # In this example, the backend is assumed to be a real device
            # on the IBM Quantum platform

            from qiskit_experiments.library.characterization import CrossResonanceHamiltonian

            qubit=63
            for qpair in backend.coupling_map:
                if qpair[0]==qubit:
                    qubits=qpair
                    print(qubits)
                    break

            exp=CrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)

            exp.set_experiment_options(
                                   min_durations=1.0e-07, 
                                   max_durations=1.2e-06, 
                                   num_durations=48, 
                                   amp=0.20, 
                                   amp_t=0.0, 
                                   sigma=30, 
                                   risefall=2)
            exp.set_run_options(shots=1000)
            print(exp.circuits()[0])
            print(exp.circuits()[287])

            step1=False # "False" shows the result saved in a previous job
            if step1==True:
                exp_data=exp.run().block_for_results()

            else:
                # retrieve your jobs
                from qiskit_ibm_provider import IBMProvider
                from qiskit_experiments.framework import ExperimentData
                # job_ids= ["cnvwmx38ybr000846y6g"] # case: shots=100
                # job_ids= ["cnvwv2c8ybr000846yz0"] # case: shots=1000
                # job_ids= ["cqccf2wqgrzg008cz3dg"] # case: ibm_kyoto, physical_qubits=(6,5), shots=100
    
                provider = IBMProvider()
                job_ids = ["cqweecsae5gg008mftw0"] # case: ibm_brisbane, physical_qubits=(63,64), shots=1000, amp=0.20
                exp_data = ExperimentData(experiment=exp)
                exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
                exp.analysis.run(exp_data)
                exp_data.block_for_results()

            result=exp_data.analysis_results()
            exp_data.figure(0)
```

### EchoedCRH
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend ibm-kyoto
            from qiskit_ibm_runtime import QiskitRuntimeService
            service = QiskitRuntimeService(channel="ibm_quantum")
            backend = service.backend("ibm_kyoto")

        ..jupyter-execute::

            # In this example, the backend is assumed to be a real device
            # on the IBM Quantum platform

            from qiskit_experiments.library.characterization import EchoedCrossResonanceHamiltonian

            qubits=(17, 30)
            exp=EchoedCrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)
            exp.set_experiment_options(
                                   min_durations=1.0e-07, 
                                   max_durations=1.2e-06, 
                                   num_durations=48, 
                                   amp=0.20, 
                                   amp_t=0.0, 
                                   sigma=30, 
                                   risefall=2)
            exp.set_run_options(shots=1000)

            step1=False # "False" shows the result saved in a previous job
            if step1==True:
                exp_data=exp.run().block_for_results()
                result=exp_data.analysis_results()
 
            else:
                # retrieve your jobs
                from qiskit_ibm_provider import IBMProvider
                from qiskit_experiments.framework import ExperimentData 
                # job_ids= ["cnwbhqe5vh500087x5fg"] # case : ibm_kyoto, shots=1000, qubits=(17, 30)
    
                provider = IBMProvider()
                job_ids = ["cqwegbs1j3r00081x4t0"] # case : ibm_kyoto, shots=1000, qubits=(17, 30), amp=0.20
                exp_data = ExperimentData(experiment=exp)
                exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
                exp.analysis.run(exp_data)
                exp_data.block_for_results()

            result=exp_data.analysis_results()
            exp_data.figure(0)
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

### EFSpectroscopy
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
            frequencies = np.linspace(freq01_estimate-15e6, freq01_estimate+15e6, 51)

            exp = EFSpectroscopy(physical_qubits = (0,),
                                     frequencies = frequencies,
                                     backend = backend,
                                     absolute = True,
                                    )
            exp.set_experiment_options(amp=0.005)

            exp_data = exp.run().block_for_results()
            exp_data.figure(0)
```

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





### FineFrequency
```
    # section: example
        .. jupyter-execute::
            # :hide-code:

            # backend
            from qiskit.providers.fake_provider import GenericBackendV2
            num_qubits=27
            backend =GenericBackendV2(num_qubits=num_qubits)

        .. jupyter-execute::
            # mwc
            from qiskit_experiments.library.characterization import FineFrequency

            qubit=0
            repetitions = list(range(40))
            exp = FineFrequency((qubit,), 
                                delay_duration=320, 
                                backend=backend, 
                                repetitions=repetitions)
            exp.set_transpile_options(optimization_level=0, basis_gates=['sx', 'rz', 'delay'])
            exp.set_run_options(shots=1000, seed_simulator=199)
            print(exp.circuits()[3])

        .. jupyter-execute::
            # mwc
            exp_data = exp.run().block_for_results()
            result = exp_data.analysis_results()
            exp_data.figure(0)
```

### FineFrequency (revised) FakeBackend
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

### EFRabi
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
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend ibm_kyoyo
            from qiskit_ibm_provider import IBMProvider
            INSTANCE="ibm-q/open/main"
            provider = IBMProvider(instance=INSTANCE)
            backend = provider.get_backend("ibm_kyoto")

            qubit = 6
            for qpair in backend.coupling_map:
                if qpair[0] == qubit:
                    break       

        ..jupyter-execute::

            qubit = 6
            from qiskit import pulse
            from qiskit_experiments.library import StarkRamseyXY

            exp = StarkRamseyXY(
                physical_qubits=(qubit,),
                backend=backend,
             )
            exp.set_experiment_options(stark_amp=0.5, 
                                       stark_channel=pulse.ControlChannel(qubit),
                                      )
            exp.set_run_options(shots=1000)

            for i in range(2):
                print(exp.circuits()[i])

            step1=False   # run carefully if your backend is a real device
            if step1==True:        
                exp_data=exp.run().block_for_results()
                result=exp_data.analysis_results()    
                for _ in result:
                    print(_)

                exp_data.figure(0)

            else:
                pass

            # retrieve your jobs
            from qiskit_experiments.framework import ExperimentData 

            job_ids= ["cnmb50mtakkg008rgkdg"] # List of job IDs for the experiment
            exp_data = ExperimentData(experiment=exp)
            exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
            exp.analysis.run(exp_data)
            exp_data.block_for_results()
            result=exp_data.analysis_results()

            exp_data.figure(0)
```

### StarkRamseyXYAmpScan
```
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend ibm_kyoyo
            from qiskit_ibm_provider import IBMProvider
            INSTANCE="ibm-q/open/main"
            provider = IBMProvider(instance=INSTANCE)
            backend = provider.get_backend("ibm_kyoto")

            qubit = 6
            for qpair in backend.coupling_map:
                if qpair[0] == qubit:
                    break       

        ..jupyter-execute::

            qubit = 6
            from qiskit import pulse
            from qiskit_experiments.library import StarkRamseyXYAmpScan

            exp = StarkRamseyXYAmpScan(
                physical_qubits=(qubit,),
                backend=backend,
                )
            exp.set_experiment_options(
                stark_channel=pulse.ControlChannel(qubit), 
                )
            exp.set_run_options(shots=100)

            for i in range(2):
                print(exp.circuits()[i])

            step1=False   # run carefully if your backend is a real device
            if step1==True:        
                exp_data=exp.run().block_for_results()
                result=exp_data.analysis_results()    
                for _ in result:
                    print(_)

                exp_data.figure(0)

            else:
                pass

            # retrieve your jobs
            from qiskit_experiments.framework import ExperimentData

            job_ids= ["cnq8b5m24wx0008fe6vg"] # List of job IDs for the experiment
            exp_data = ExperimentData(experiment=exp)
            exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
            exp.analysis.run(exp_data)
            exp_data.block_for_results()
            result=exp_data.analysis_results()

            exp_data.figure(0)
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
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend ibm-kyoto
            from qiskit_ibm_runtime import QiskitRuntimeService
            service = QiskitRuntimeService(channel="ibm_quantum") 
            backend = service.backend("ibm_kyoto")

        ..jupyter-execute::

            # In this example, the backend is assumed to be a real device
            # on the IBM Quantum platform

            import numpy as np
            from qiskit_experiments.library.characterization import ResonatorSpectroscopy

            qubit = 107
            exp = ResonatorSpectroscopy(physical_qubits = (qubit,), backend = backend)
            exp.set_run_options(shots=1000, seed_simulator=118)            
            print(exp.circuits()[0])

            step1=False  # "False" shows the result saved in a previous job
            if step1==True:
                exp_data = exp.run().block_for_results()
  
            else:
                # retrieve your jobs
                from qiskit_ibm_provider import IBMProvider
                from qiskit_experiments.framework import ExperimentData
    
                provider=IBMProvider()
                job_ids= ["cqs4qjy88ev00080zbj0"]
    
                exp_data = ExperimentData(experiment=exp)
                exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
                exp.analysis.run(exp_data)
                exp_data.block_for_results()

            result = exp_data.analysis_results()
            exp_data.figure(0)
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
    # section: example
        ..jupyter-execute::
            :hide-code:

            # backend ibm_brisbane
            from qiskit_ibm_provider import IBMProvider
            INSTANCE="ibm-q/open/main"
            provider = IBMProvider(instance=INSTANCE)
            backend = provider.get_backend("ibm_brisbane")

        ..jupyter-execute::

            from qiskit_experiments.library.characterization.t1 import StarkP1Spectroscopy

            qubit=13
            exp = StarkP1Spectroscopy((qubit,), backend=backend)
            exp.set_run_options(shots=100)

            step1=False # run carefully if your device is a real device
            if step1==True:
                exp_data = exp.run().block_for_results()
                result=exp_data.analysis_results()
                for _ in result:
                    print(_)

                exp_data.figure(0)

            else:
                pass

            # retrieve your jobs
            from qiskit_experiments.framework import ExperimentData

            job_ids= ["cnkf59nq9ax00087ecv0"] # list of job IDs for the experiment
            exp_data = ExperimentData(experiment = exp)
            exp_data.add_jobs([provider.retrieve_job(job_id) for job_id in job_ids])
            exp.analysis.run(exp_data)
            exp_data.block_for_results()
            result=exp_data.analysis_results()

            exp_data.figure(0)
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


