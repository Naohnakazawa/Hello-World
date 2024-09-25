### Question
Where is the definition of the parameter for the static ZZ coupling strength, f_zz ?

```
f_zz ~ 1e4 ibm_kyoto
f_zz ~ 1e2 AerSimulator.from_backend(GenericBackendV2())
```

### Circuits in ZZRamsey-experiment
```
#backend
from qiskit.providers.fake_provider import GenericBackendV2

seed = 55
backend = GenericBackendV2(num_qubits=2,
                           basis_gates=["id","rz","sx","x","cx","reset","delay","measure"],
                           coupling_map=[[0,1],],
                           calibrate_instructions=True,
                           seed=seed,
                          )

# mwc
import numpy as np
from qiskit import pulse
from qiskit import QuantumCircuit
from qiskit.circuit import Gate, Parameter
from qiskit.pulse import ScheduleBlock
from qiskit.compiler import transpile, schedule
from qiskit.pulse import InstructionScheduleMap

import qiskit.quantum_info as qi
from qiskit.visualization import plot_histogram, plot_state_city

min_delay = 0e-6
max_delay = 10e-6
num_delays = 50
num_rotations = 5

delays = np.linspace(min_delay, max_delay, num_delays).tolist()

freq = num_rotations / (max(delays) - min(delays))

def template_circuits(frequency):
    """Template circuits for series 0 and 1 parameterized by delay

        The generated circuits have the length of the delay instructions as the
        only parameter.

        Args:
            dt_value: the value of the backend ``dt`` value. Used to convert
                delay values into units of seconds.
            delay_unit: the unit of circuit delay instructions.

        Returns:
            Circuits for series 0 and 1
        """
    
    delay = Parameter("delay")    
    delay_freq = timing.dt*frequency
    
    # Template circuit for series 0
    # Control qubit starting in |0> state, flipping to |1> in middle
    circ0 = QuantumCircuit(2)
    circ0.metadata["series"] = "0"

    circ0.sx(0)

    circ0.barrier()
    circ0.delay(delay, 0, timing.delay_unit)
    circ0.barrier()

    circ0.x(0)
    circ0.x(1)

    circ0.barrier()
    circ0.delay(delay, 0, timing.delay_unit)
    circ0.barrier()

    circ0.rz(2 * np.pi * delay_freq * (2 * delay), 0)
    circ0.sx(0)
    # Flip control back to 0, so control qubit is in 0 for all circuits
    # when qubit 1 is measured.
    circ0.x(1)

    # Template circuit for series 1
    # Control qubit starting in |1> state, flipping to |0> in middle
    circ1 = QuantumCircuit(2)
    circ1.metadata["series"] = "1"

    circ1.x(1)
    circ1.sx(0)

    circ1.barrier()
    circ1.delay(delay, 0, timing.delay_unit)
    circ1.barrier()

    circ1.x(0)
    circ1.x(1)

    circ1.barrier()
    circ1.delay(delay, 0, timing.delay_unit)
    circ1.barrier()

    circ1.rz(2 * np.pi * delay_freq * (2 * delay), 0)
    circ1.sx(0)

    return circ0, circ1

circ0, circ1 = template_circuits(frequency=freq)

circs = []
for delay in delays:
    for circ in (circ0, circ1):
        circ_assigned = circ.assign_parameters(
            {circ.parameters[0]: timing.round_delay(time=delay / 2)}, inplace=False
            )
        circ_assigned.metadata["xval"] = 2 * timing.delay_time(time=delay / 2)
        circs.append(circ_assigned)
        
circ_sched_0 = schedule(circs[80], backend=backend)
display(circ_sched_0.draw())
       
circ_sched_1 = schedule(circs[81], backend=backend)
display(circ_sched_1.draw())


rho_0 = qi.DensityMatrix.from_instruction(circs[80])
display(plot_state_city(rho_0, "DensityMatrix_circs[80]"))


rho_1 = qi.DensityMatrix.from_instruction(circs[81])
display(plot_state_city(rho_1, "DensityMatrix_circs[81]"))
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a81e316e-bbe5-411f-b485-4541f781cf03)







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/c6abd7d1-ea7a-42ab-bd10-306ea51261ad)







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/f9cb9c9b-3707-4de6-981c-9888d21081fa)







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/293c40be-c40f-4cdd-a941-3fbfb355e1a9)







### ZZRamsey-experiment-local-Aer(GBV2)
```
# test

#backend
from qiskit.providers.fake_provider import GenericBackendV2
from qiskit_aer import AerSimulator

seed = 55
fake_backend = GenericBackendV2(num_qubits=2,                                
                                basis_gates=["id","rz","sx","x","cx","reset","delay","measure"],
                                coupling_map=[[0,1],],
                                seed=seed,
                               )

backend = AerSimulator.from_backend(fake_backend)

# mwc
from qiskit_experiments.library.characterization import ZZRamsey

qubits = (0, 1)
exp = ZZRamsey(physical_qubits=qubits, backend=backend)

exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/11d85879-c41b-4bc1-81e6-921ec2ecd0e5)






### ZZRamsey (backend=ibm_kyoto)
```
# backend
from qiskit_ibm_runtime import QiskitRuntimeService
from qiskit_experiments.framework import ExperimentData

service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_kyoto")

num_qubits = 1
qubit = 0

qubit=63
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        qubits=qpair
        print(qubits)
        break

# mwc

from qiskit_experiments.library.characterization import ZZRamsey

backend.open_session()
qubits = (63, 64)
exp = ZZRamsey(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=100)

print(exp.circuits()[2]) #0319
print(exp.circuits()[3]) #0319
print(len(exp.circuits()))
    
exp_data = exp.run().block_for_results()
display(exp_data.figure(0))
exp_data.analysis_results(dataframe=True)

backend.close_session()
    
# retrieve your jobs
# List of job IDs for the experiment
job_ids= ["ct4gmeh7srd00088rbk0"]

experiment = exp
expdata = ExperimentData(experiment = experiment)
expdata.add_data([service.job(job_id).result() for job_id in job_ids])
experiment.analysis.run(expdata, replace_results=True)

# Block execution of subsequent code until analysis is complete
expdata.block_for_results()

display(expdata.figure(0))
expdata.analysis_results(dataframe=True)
```







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/1f2b5440-2c33-414f-a028-66ed70e9e00f)








![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/091e9cf8-6155-4661-9be0-90774e7eff75)






### ZZRamsey (DynamicsBackend)
```
# backend = dynamics_backend

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

from qiskit.circuit.library import XGate, SXGate, RZGate, CXGate, ECRGate, HGate
# from qiskit.circuit.instructions import Delay
from qiskit.circuit import Parameter, Delay
from qiskit.providers.backend import QubitProperties

target = backend.target

# qubit properties
target.qubit_properties = [QubitProperties(frequency=v0), QubitProperties(frequency=v1)]

# add instructions
target.add_instruction(XGate(), properties={(0,): None, (1,): None})
target.add_instruction(SXGate(), properties={(0,): None, (1,): None})

target.add_instruction(ECRGate(), properties={(0, 1): None, (1, 0): None})

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
    properties={(0,): InstructionProperties(calibration=rz0), (1,): InstructionProperties(calibration=rz1)}
)

# Add Delay instruction for the ZZRamsey characterization experiments
duration = Parameter("dur")
target.add_instruction(Delay(duration), properties={(0,): None, (1,): None})

print(f"The value of dt is {backend.dt}.")
print(target)

### initialization of Calibrations object
import pandas as pd
from qiskit_experiments.calibration_management.calibrations import Calibrations
from qiskit_experiments.calibration_management.basis_gate_library import FixedFrequencyTransmon, EchoedCrossResonance

cals = Calibrations(libraries=[FixedFrequencyTransmon(basis_gates=['x', 'sx']),
                               EchoedCrossResonance(basis_gates=["cr45p", "cr45m", "ecr", "rzx"])
                              ])

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

display(pd.DataFrame(**cals.parameters_table(qubit_list=[(0, )])))
display(pd.DataFrame(**cals.parameters_table(qubit_list=[(1, )])))

### contral channel map
# set the control channel map
backend.set_options(control_channel_map={(0, 1): 0, (1, 0): 1})

target.update_from_instruction_schedule_map(cals.get_inst_map())
print(target)

# mwc

from qiskit_experiments.library.characterization import ZZRamsey

qubits = (0, 1)
exp = ZZRamsey(physical_qubits=qubits, backend=backend)
exp.set_run_options(shots=1000, seed_simulator=105)
print(exp.transpile_options)
print(exp._default_transpile_options())

display(exp.circuits()[0].draw())
print(exp.circuits()[0].calibrations) 
display(exp._transpiled_circuits()[0].draw())

display(exp.circuits()[2].draw())
print(exp.circuits()[2].calibrations) 

exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()
exp_data.figure(0)
```






![image](https://github.com/user-attachments/assets/90b4de60-a4c2-4ec3-a5bc-5c9972eddba1)












