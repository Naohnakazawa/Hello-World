
# Characterization experiments

### CorrelatedReadoutError
```
```

### CrossResonanceHamiltonian (new PR)
```
```

### EchoedCRH (new PR)
```
```

### RoughDrag
```
```

### EFSpectroscopy(new PR, revised 20240629)
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
```





### FineXAmplitude
```
```

### FineSXAmplitude
```
```

### FineDrag
```
```

### FineXDrg
```
```

### FineSXDrag
```
```

### FineFrequency
```
```

### HalfAngle
```
```

### LocalReadoutError
```
```

### MultiStateDiscrimination
```
```

### QibitSpectroscopy
```
```

### Rabi
```
```

### EFRabi(new PR, this code (published version) may be wrong!. revised 20240705)
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
```

### ResonatorSpectroscopy
```
```

### T1
```
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

