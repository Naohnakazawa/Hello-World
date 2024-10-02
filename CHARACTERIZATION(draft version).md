
# Characterization experiments

### CorrelatedReadoutError
```
```

### CrossResonanceHamiltonian (new PR)
```
### backend = dynamics_backend

# Configure JAX
import jax
jax.config.update("jax_enable_x64", True)
jax.config.update("jax_platform_name", "cpu")

### a 2-qubit fixed-frequency transmon model with fixed coupling
import numpy as np

dim = 3

v0 = 4.86e9
anharm0 = -0.32e9
r0 = 0.22e9

v1 = 4.97e9
anharm1 = -0.32e9
r1 = 0.26e9

J = 0.002e9

a = np.diag(np.sqrt(np.arange(1, dim)), 1)
adag = np.diag(np.sqrt(np.arange(1, dim)), -1)
N = np.diag(np.arange(dim))

ident = np.eye(dim, dtype=complex)
full_ident = np.eye(dim**2, dtype=complex)

N0 = np.kron(ident, N)
N1 = np.kron(N, ident)

a0 = np.kron(ident, a)
a1 = np.kron(a, ident)

a0dag = np.kron(ident, adag)
a1dag = np.kron(adag, ident)


static_ham0 = 2 * np.pi * v0 * N0 + np.pi * anharm0 * N0 * (N0 - full_ident)
static_ham1 = 2 * np.pi * v1 * N1 + np.pi * anharm1 * N1 * (N1 - full_ident)

static_ham_full = static_ham0 + static_ham1 + 2 * np.pi * J * ((a0 + a0dag) @ (a1 + a1dag))

drive_op0 = 2 * np.pi * r0 * (a0 + a0dag)
drive_op1 = 2 * np.pi * r1 * (a1 + a1dag)

### definiton of the solver
from qiskit_dynamics import Solver

# build solver
dt = 1/4.5e9

solver = Solver(
    static_hamiltonian=static_ham_full,
    hamiltonian_operators=[drive_op0, drive_op1, drive_op0, drive_op1],
    rotating_frame=static_ham_full,
    hamiltonian_channels=["d0", "d1", "u0", "u1"],
    channel_carrier_freqs={"d0": v0, "d1": v1, "u0": v1, "u1": v0},
    dt=dt,
    array_library="jax",
)

### backend
from qiskit_dynamics import DynamicsBackend

# Consistent solver option to use throughout notebook
solver_options = {"method": "jax_odeint", "atol": 1e-6, "rtol": 1e-8, "hmax": dt}

backend = DynamicsBackend(
    solver=solver,
    subsystem_dims=[dim, dim], # for computing measurement data
    solver_options=solver_options, # to be used every time run is called
)

### backend.target
from qiskit import pulse
from qiskit.transpiler import InstructionProperties

from qiskit.circuit.library import XGate, SXGate, RZGate, CXGate
from qiskit.circuit import Parameter
from qiskit.providers.backend import QubitProperties

target = backend.target

# qubit properties
target.qubit_properties = [QubitProperties(frequency=v0), QubitProperties(frequency=v1)]

# add instructions
target.add_instruction(XGate(), properties={(0,): None, (1,): None})
target.add_instruction(SXGate(), properties={(0,): None, (1,): None})

target.add_instruction(CXGate(), properties={(0, 1): None, (1, 0): None})

# Add RZ instruction as phase shift for drag cal
phi = Parameter("phi")
with pulse.build() as rz0:
    pulse.shift_phase(phi, pulse.DriveChannel(0))
    pulse.shift_phase(phi, pulse.ControlChannel(1))

with pulse.build() as rz1:
    pulse.shift_phase(phi, pulse.DriveChannel(1))
    pulse.shift_phase(phi, pulse.ControlChannel(0))

target.add_instruction(
    RZGate(phi),
    {(0,): InstructionProperties(calibration=rz0), (1,): InstructionProperties(calibration=rz1)}
)

### initialization of Calibrations object
import pandas as pd
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

cals = Calibrations(libraries=[FixedFrequencyTransmon(basis_gates=['x', 'sx'])])

display(pd.DataFrame(**cals.parameters_table(qubit_list=[0, ()])))

### calibrations of "amp" and "beta" in "sx" and "x" gates
from qiskit_experiments.library.calibration import RoughXSXAmplitudeCal

# rabi experiments for qubit 0
rabi0 = RoughXSXAmplitudeCal([0], cals, backend=backend, amplitudes=np.linspace(-0.2, 0.2, 27))

# rabi experiments for qubit 1
rabi1 = RoughXSXAmplitudeCal([1], cals, backend=backend, amplitudes=np.linspace(-0.2, 0.2, 27))

# %%time
rabi0_data = rabi0.run().block_for_results()
rabi1_data = rabi1.run().block_for_results()

from qiskit_experiments.library.calibration import RoughDragCal

cal_drag0 = RoughDragCal([0], cals, backend=backend, betas=np.linspace(-20, 20, 15))
cal_drag1 = RoughDragCal([1], cals, backend=backend, betas=np.linspace(-20, 20, 15))

cal_drag0.set_experiment_options(reps=[3, 5, 7])
cal_drag1.set_experiment_options(reps=[3, 5, 7])

# %%time
drag0_data = cal_drag0.run().block_for_results()
drag1_data = cal_drag1.run().block_for_results()

display(pd.DataFrame(**cals.parameters_table(qubit_list=[0, ()])))
display(pd.DataFrame(**cals.parameters_table(qubit_list=[1, ()])))

### contral channel map
# set the control channel map
backend.set_options(control_channel_map={(0, 1): 0, (1, 0): 1})

# mwc

from qiskit_experiments.library import CrossResonanceHamiltonian

cr_ham_experiment = CrossResonanceHamiltonian(
    physical_qubits=(0, 1),
    durations=np.linspace(1e-7, 1e-6, 17),
    backend=backend
)

backend.target.update_from_instruction_schedule_map(cals.get_inst_map())
display(cr_ham_experiment.circuits()[10].draw("mpl"))

# %time 
data_cr = cr_ham_experiment.run().block_for_results()

data_cr.figure(0)
```

### EchoedCRH (new PR)
```

### backend = dynamics_backend

# Configure JAX
import jax
jax.config.update("jax_enable_x64", True)
jax.config.update("jax_platform_name", "cpu")

### a 2-qubit fixed-frequency transmon model with fixed coupling
import numpy as np

dim = 3

v0 = 4.86e9
anharm0 = -0.32e9
r0 = 0.22e9

v1 = 4.97e9
anharm1 = -0.32e9
r1 = 0.26e9

J = 0.002e9

a = np.diag(np.sqrt(np.arange(1, dim)), 1)
adag = np.diag(np.sqrt(np.arange(1, dim)), -1)
N = np.diag(np.arange(dim))

ident = np.eye(dim, dtype=complex)
full_ident = np.eye(dim**2, dtype=complex)

N0 = np.kron(ident, N)
N1 = np.kron(N, ident)

a0 = np.kron(ident, a)
a1 = np.kron(a, ident)

a0dag = np.kron(ident, adag)
a1dag = np.kron(adag, ident)


static_ham0 = 2 * np.pi * v0 * N0 + np.pi * anharm0 * N0 * (N0 - full_ident)
static_ham1 = 2 * np.pi * v1 * N1 + np.pi * anharm1 * N1 * (N1 - full_ident)

static_ham_full = static_ham0 + static_ham1 + 2 * np.pi * J * ((a0 + a0dag) @ (a1 + a1dag))

drive_op0 = 2 * np.pi * r0 * (a0 + a0dag)
drive_op1 = 2 * np.pi * r1 * (a1 + a1dag)

### definiton of the solver
from qiskit_dynamics import Solver

# build solver
dt = 1/4.5e9

solver = Solver(
    static_hamiltonian=static_ham_full,
    hamiltonian_operators=[drive_op0, drive_op1, drive_op0, drive_op1],
    rotating_frame=static_ham_full,
    hamiltonian_channels=["d0", "d1", "u0", "u1"],
    channel_carrier_freqs={"d0": v0, "d1": v1, "u0": v1, "u1": v0},
    dt=dt,
    array_library="jax",
)

### backend
from qiskit_dynamics import DynamicsBackend

# Consistent solver option to use throughout notebook
solver_options = {"method": "jax_odeint", "atol": 1e-6, "rtol": 1e-8, "hmax": dt}

backend = DynamicsBackend(
    solver=solver,
    subsystem_dims=[dim, dim], # for computing measurement data
    solver_options=solver_options, # to be used every time run is called
)

### backend.target
from qiskit import pulse
from qiskit.transpiler import InstructionProperties

from qiskit.circuit.library import XGate, SXGate, RZGate, CXGate
from qiskit.circuit import Parameter
from qiskit.providers.backend import QubitProperties

target = backend.target

# qubit properties
target.qubit_properties = [QubitProperties(frequency=v0), QubitProperties(frequency=v1)]

# add instructions
target.add_instruction(XGate(), properties={(0,): None, (1,): None})
target.add_instruction(SXGate(), properties={(0,): None, (1,): None})

target.add_instruction(CXGate(), properties={(0, 1): None, (1, 0): None})

# Add RZ instruction as phase shift for drag cal
phi = Parameter("phi")
with pulse.build() as rz0:
    pulse.shift_phase(phi, pulse.DriveChannel(0))
    pulse.shift_phase(phi, pulse.ControlChannel(1))

with pulse.build() as rz1:
    pulse.shift_phase(phi, pulse.DriveChannel(1))
    pulse.shift_phase(phi, pulse.ControlChannel(0))

target.add_instruction(
    RZGate(phi),
    {(0,): InstructionProperties(calibration=rz0), (1,): InstructionProperties(calibration=rz1)}
)

### initialization of Calibrations object
import pandas as pd
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon

cals = Calibrations(libraries=[FixedFrequencyTransmon(basis_gates=['x', 'sx'])])

display(pd.DataFrame(**cals.parameters_table(qubit_list=[0, ()])))

### calibrations of "amp" and "beta" in "sx" and "x" gates
from qiskit_experiments.library.calibration import RoughXSXAmplitudeCal

# rabi experiments for qubit 0
rabi0 = RoughXSXAmplitudeCal([0], cals, backend=backend, amplitudes=np.linspace(-0.2, 0.2, 27))

# rabi experiments for qubit 1
rabi1 = RoughXSXAmplitudeCal([1], cals, backend=backend, amplitudes=np.linspace(-0.2, 0.2, 27))

# %%time
rabi0_data = rabi0.run().block_for_results()
rabi1_data = rabi1.run().block_for_results()

from qiskit_experiments.library.calibration import RoughDragCal

cal_drag0 = RoughDragCal([0], cals, backend=backend, betas=np.linspace(-20, 20, 15))
cal_drag1 = RoughDragCal([1], cals, backend=backend, betas=np.linspace(-20, 20, 15))

cal_drag0.set_experiment_options(reps=[3, 5, 7])
cal_drag1.set_experiment_options(reps=[3, 5, 7])

# %%time
drag0_data = cal_drag0.run().block_for_results()
drag1_data = cal_drag1.run().block_for_results()

display(pd.DataFrame(**cals.parameters_table(qubit_list=[0, ()])))
display(pd.DataFrame(**cals.parameters_table(qubit_list=[1, ()])))

### contral channel map
# set the control channel map
backend.set_options(control_channel_map={(0, 1): 0, (1, 0): 1})

# mwc

from qiskit_experiments.library import EchoedCrossResonanceHamiltonian

ecr_ham_experiment = EchoedCrossResonanceHamiltonian(
    physical_qubits=(0, 1),
    durations=np.linspace(1e-7, 1e-6, 17),
    backend=backend
)

backend.target.update_from_instruction_schedule_map(cals.get_inst_map())
display(ecr_ham_experiment.circuits()[0].draw("mpl"))

display(ecr_ham_experiment.circuits()[1].draw("mpl"))

display(ecr_ham_experiment.circuits()[2].draw("mpl"))

display(ecr_ham_experiment.circuits()[3].draw("mpl"))

display(ecr_ham_experiment.circuits()[4].draw("mpl"))

display(ecr_ham_experiment.circuits()[5].draw("mpl"))

display(ecr_ham_experiment.circuits()[-6].draw("mpl"))

display(ecr_ham_experiment.circuits()[-5].draw("mpl"))

display(ecr_ham_experiment.circuits()[-4].draw("mpl"))

display(ecr_ham_experiment.circuits()[-3].draw("mpl"))

display(ecr_ham_experiment.circuits()[-2].draw("mpl"))

display(ecr_ham_experiment.circuits()[-1].draw("mpl"))

# %time 
data_ecr = ecr_ham_experiment.run().block_for_results()

data_ecr.figure(0)
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
            display(exp_data.figure(0))
            exp_data.analysis_results(dataframe=True)
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

### EFRabi(new PR, the published version must be replaced with this revised 20240705)
```
# section: example
..jupyter-execute::
:hide-code:

# backend
from qiskit_experiments.test import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5e9, -0.25e9, 1e9, 0.8e9, 1e4, noise=True)

..jupyter-execute::

# mwc
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

### StarkRamseyXY (This code must be improved)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_ibm_runtime import QiskitRuntimeService
service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

qubit = 6
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        break       

# ..jupyter-execute::
        
# mwc        
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

job_ids= ["cnmb50mtakkg008rgkdg"] # list of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([service.job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data, replace_results=True)
exp_data.block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)
```




![image](https://github.com/user-attachments/assets/3e2b4f43-77f4-4b96-bfc3-8b05d867f386)








### StarkRamseyXYAmpScan (This code must be improved)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_kyoyo
from qiskit_ibm_runtime import QiskitRuntimeService
service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

qubit = 6
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        break       

# ..jupyter-execute::
        
# mwc        
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
from qiskit_experiments.framework import ExperimentData # retrieve jove

job_ids= ["cnq8b5m24wx0008fe6vg"] # List of job IDs for the experiment
exp_data = ExperimentData(experiment=exp)
exp_data.add_jobs([service.job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data, replace_results=True)
exp_data.block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)  
```





![image](https://github.com/user-attachments/assets/ee7a5029-5daa-4be4-b544-f3f19dce96a8)





### StarkRamseyXYAmpScan (revised 20241002, new PR in DrivenFrequencyTuning Experiments)
```
# section: example
# ..jupyter-execute::
# :hide-code:
# backend

from qiskit_experiments.test.pulse_backend import SingleTransmonTestBackend
backend = SingleTransmonTestBackend(5.2e9,-.25e9, 1e9, 0.8e9, 1e4, noise=False, seed=198)

qubit = 0

# ..jupyter-execute::
# mwc

# StarkRamseyXYAmpScan, see "manual-..."
# This option is not default values of parameters

import numpy as np
from qiskit import pulse
from qiskit.pulse.channels import PulseChannel, DriveChannel
from qiskit_experiments.library.driven_freq_tuning.p1_spect import StarkP1Spectroscopy
from qiskit_experiments.library.driven_freq_tuning import StarkRamseyXYAmpScan

exp = StarkRamseyXYAmpScan((qubit,), backend=backend)
exp.set_experiment_options(
            stark_channel=pulse.ControlChannel(qubit),
            stark_freq_offset=80e6,
            stark_sigma=15e-9,
            stark_risefall=2,
            stark_length=50e-9,
            min_stark_amp=-1.0,
            max_stark_amp=1.0,
            num_stark_amps=51,
            stark_amps=None,
        )

exp.set_run_options(shots=1000)
exp_data = exp.run().block_for_results()
result = exp_data.analysis_results()
print(result)
display(exp_data.figure(0))

ret_coeffs = exp_data.analysis_results("stark_coefficients").value

# encode the stark_coefficients for the StarkP1Spectroscopy experiment
import json
from qiskit_experiments.framework import ExperimentEncoder

with open("coefficients.json", "w") as fp:
    json.dump(ret_coeffs, fp, cls=ExperimentEncoder)
```





![image](https://github.com/user-attachments/assets/4f857c34-8384-49b3-873d-80676f97daba)










### ReadoutAngle
```
```

### ResonatorSpectroscopy (This code must be improved)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend

from qiskit_ibm_runtime import QiskitRuntimeService
service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

# ..jupyter-execute::

# mwc
import numpy as np
from qiskit_experiments.library.characterization import ResonatorSpectroscopy

qubit = 107
exp = ResonatorSpectroscopy(physical_qubits = (qubit,), backend = backend)
exp.set_run_options(shots=1000)
print(exp.circuits()[0])

step1=False  # "False" shows the result saved in a previous job
if step1==True:
    exp_data = exp.run().block_for_results()

else:
    # retrieve your jobs
    from qiskit_experiments.framework import ExperimentData
    job_ids= ["cr1p6bspcdbg008trhy0"] # case : ibm_kyoto, shots=1000, qubits=(107,)

    exp_data = ExperimentData(experiment=exp)
    exp_data.add_jobs([service.job(job_id) for job_id in job_ids])
    exp.analysis.run(exp_data, replace_results=True)
    exp_data.block_for_results()

result = exp_data.analysis_results()
exp_data.figure(0)
```




![image](https://github.com/user-attachments/assets/4824bf4c-ab8a-484b-862d-78c026ce5f27)






### T1
```
```

### StarkP1Spectroscopy (This code must be improved)
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_ibm_runtime import QiskitRuntimeService
service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

# ..jupyter-execute::

# mwc
import numpy as np
from qiskit import pulse
from qiskit.pulse.channels import PulseChannel, DriveChannel
from qiskit_experiments.library.driven_freq_tuning.p1_spect import StarkP1Spectroscopy

qubit=13
exp = StarkP1Spectroscopy((qubit,),
                          backend=backend,
                          t1_delay=20.0e-6,
                          stark_channel=DriveChannel(qubit),                          
                         )
exp.set_run_options(shots=1000)

step1=False # run carefully if your device is a real one
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
job_ids=["cnx1bf5syp800084fwfg"] # case : shots=1000

exp_data = ExperimentData(experiment = exp)
exp_data.add_jobs([service.job(job_id) for job_id in job_ids])
exp.analysis.run(exp_data, replace_results=True)
exp_data.block_for_results() 
result=exp_data.analysis_results()

exp_data.figure(0)
```



![image](https://github.com/user-attachments/assets/c726a03a-293e-4097-93ad-af7a5785797c)







### StarkP1Spectroscopy (revised 20241002, new PR in DrivenFrequencyTuning Experiments)
```
# after the StarkRamseyXYAmpScan experimnt
# ...

# ..jupyter-execute::
# mwc

# decode the stark_coefficients determined by the StarkRamseyXYAmpScan experiment
from qiskit_experiments.framework import ExperimentDecoder

with open("coefficients.json", "r") as fp:
    coefficients = json.load(fp, cls=ExperimentDecoder)

# StarkP1Spectroscopy
exp = StarkP1Spectroscopy(physical_qubits=(qubit,),
                          backend=backend,
                          stark_channel=pulse.DriveChannel(qubit),                          
                         )
exp.set_experiment_options(
    t1_delay=20e-6,
    min_xval=-20e6,
    max_xval=20e6,
    xval_type="frequency",
    spacing="linear",
    stark_coefficients=coefficients,
)

exp.set_run_options(shots=100)
display(exp.circuits()[0].draw("mpl"))

exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()
for _ in result:
    print(_)

display(exp_data.figure(0))
```







![image](https://github.com/user-attachments/assets/cef47a71-5b76-47a4-9f8a-441a99f18b5c)








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

