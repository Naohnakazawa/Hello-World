### fidelity of RZX(np.pi / 2) backend=DB
```
# backend = dynamics_backend(2Q, noise-less)
# the backend is calibratied for single qubit gates
# Note, ecr-gate is not calibrated.

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

from qiskit.circuit.library import XGate, SXGate, RZGate, CXGate, ECRGate, HGate, RZXGate
from qiskit.circuit import Parameter, Delay # Be careful!
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

# Add RZX instruction
theta = Parameter("theta")
target.add_instruction(RZXGate(theta), properties={(0, 1): None, (1, 0): None})

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

### calibrations of "amp" in "sx" and "x" gates with FineAmplitude experiments
from qiskit_experiments.library import FineXAmplitudeCal, FineSXAmplitudeCal

# cal "x"
cal_finexamp0 = FineXAmplitudeCal([0], cals, "x", backend, "amp", auto_update=True)
cal_finexamp1 = FineXAmplitudeCal([1], cals, "x", backend, "amp", auto_update=True)

cal_finexamp0_data = cal_finexamp0.run().block_for_results()
cal_finexamp1_data = cal_finexamp1.run().block_for_results()

# cal "sx"
cal_finesxamp0 = FineSXAmplitudeCal([0], cals, "sx", backend, "amp", auto_update=True)
cal_finesxamp1 = FineSXAmplitudeCal([1], cals, "sx", backend, "amp", auto_update=True)

cal_finesxamp0_data = cal_finesxamp0.run().block_for_results()
cal_finesxamp1_data = cal_finesxamp1.run().block_for_results()

### calibrations of "beta" in "sx" and "x" gates with FineDrag experiments
from qiskit_experiments.library import FineXDragCal, FineSXDragCal

# cal "x"　
cal_finexdrag0 = FineXDragCal([0], cals, backend, "β", auto_update=True)
cal_finexdrag1 = FineXDragCal([1], cals, backend, "β", auto_update=True)

cal_finexdrag0_data = cal_finexdrag0.run().block_for_results()
cal_finexdrag1_data = cal_finexdrag1.run().block_for_results()

# cal "sx"
cal_finesxdrag0 = FineSXDragCal([0], cals, backend, "β", auto_update=True)
cal_finesxdrag1 = FineSXDragCal([1], cals, backend, "β", auto_update=True)

cal_finesxdrag0_data = cal_finesxdrag0.run().block_for_results()
cal_finesxdrag1_data = cal_finesxdrag1.run().block_for_results()

### Frequencies calibration (RoughFrequencyCal)
from qiskit_experiments.library.calibration.rough_frequency import RoughFrequencyCal

freq0_est = backend.qubit_properties(0).frequency
freq1_est = backend.qubit_properties(1).frequency

frequencies_0 = np.linspace(freq0_est-15e6, freq0_est+15e6, 51)
frequencies_1 = np.linspace(freq1_est-15e6, freq1_est+15e6, 51)

cal_rough_freq0 = RoughFrequencyCal([0], cals, frequencies_0, backend=backend)
cal_rough_freq1 = RoughFrequencyCal([1], cals, frequencies_1, backend=backend)

cal_rough_freq0_data = cal_rough_freq0.run().block_for_results()
cal_rough_freq1_data = cal_rough_freq1.run().block_for_results()

### Frequencies calibration (FineFrequencyCal)
from qiskit_experiments.library.calibration.fine_frequency_cal import FineFrequencyCal

cal_freq0 = FineFrequencyCal([0], cals, backend=backend, auto_update=True, gate_name="sx") 
cal_freq1 = FineFrequencyCal([1], cals, backend=backend, auto_update=True, gate_name="sx") 

cal_freq0_data=cal_freq0.run().block_for_results()
cal_freq1_data=cal_freq1.run().block_for_results()

# parameters after calibrations
display(pd.DataFrame(**cals.parameters_table(qubit_list=[(0, )])))
display(pd.DataFrame(**cals.parameters_table(qubit_list=[(1, )])))

### contral channel map
# set the control channel map
backend.set_options(control_channel_map={(0, 1): 0, (1, 0): 1})

target.update_from_instruction_schedule_map(cals.get_inst_map())
print(target)

# rzx(np.pi / 2)-gate

qubit = 0
for qpair in backend.coupling_map:
    if qpair[0] == qubit:
        qubits = qpair
        print(qubits)
        break

print(backend.control_channel(qubits)[0])    

import numpy as np
from qiskit import pulse
from qiskit.circuit import Parameter

# calibrated on DB (qubit = 0)
dur = 160
amp = 0.119
sigma = 40
beta = -1.62
angle = 0.0

chan = pulse.DriveChannel(qubit)

with pulse.build(name="x") as x_180p:
    pulse.play(pulse.Drag(duration=dur, amp=amp, sigma=sigma, beta=beta, angle=angle), chan)

# parameters for CrossResonance tone
tgt_amp = Parameter("tgt_amp")
tgt_angle = Parameter("tgt_angle")
sigma = Parameter("σ")
cr_amp = Parameter("amp")
cr_angle = Parameter("angle")
cr_dur = Parameter("duration")

# values of parameters for CrossResonance tone "cr45p" and "cr45m"
# these values are those of ibm_brisbane, qubits=(18, 19), backend.control_channel(qubits)[0] = ControlChannel(39)
#
# tgt_amp = 0.037
# tgt_angle = 0.021
# sigma = 32
# cr_amp = 0.10
# cr_angle = -1.1
# cr_dur = 600
# width = 472

# default
tgt_amp = 0.0
tgt_angle = 0.0
sigma = 64
cr_amp = 0.31
cr_angle = 0.0
cr_dur = 1168
width = 520

t_chan = pulse.DriveChannel(qubits[1])
u_chan = backend.control_channel(qubits)[0]  

with pulse.build(name="cr45p") as cr45p:
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            cr_amp,
            angle=cr_angle,
            width=width,
            sigma=sigma,
        ),
        u_chan,
    )
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            tgt_amp,
            angle=tgt_angle,
            width=width,
            sigma=sigma,
        ),
        t_chan,
    )

with pulse.build(name="cr45m") as cr45m:
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            cr_amp,
            angle=cr_angle + np.pi,
            width=width,
            sigma=sigma,
        ),
        u_chan,
    )
    pulse.play(
        pulse.GaussianSquare(
            cr_dur,
            tgt_amp,
            angle=tgt_angle + np.pi,
            width=width,
            sigma=sigma,
        ),
        t_chan,
    )

with pulse.build(name="szx") as your_rzx:
    with pulse.align_sequential():
        pulse.reference("cr45p", "q0", "q1")
        pulse.reference("x", "q0")
        pulse.reference("cr45m", "q0", "q1")
        pulse.reference("x", "q0")

your_rzx.assign_references(subroutine_dict={("cr45p", "q0", "q1"): cr45p})
your_rzx.assign_references(subroutine_dict={("x", "q0"): x_180p})
your_rzx.assign_references(subroutine_dict={("cr45m", "q0", "q1"): cr45m})
display(your_rzx.draw())

from qiskit.circuit import QuantumCircuit, Gate
from qiskit.transpiler import InstructionProperties

szx_gate = Gate("szx", 2, [])
y_szx = {(0, 1): InstructionProperties(duration=1520 * backend.dt, error=0.001, calibration=your_rzx),
         (1, 0): InstructionProperties(duration=1520 * backend.dt, error=0.005, calibration=your_rzx)}

backend.target.add_instruction(szx_gate, y_szx)
print(backend.target)

# a fidelity test of 2-qubits gates, rzx(np.pi / 2), the instruction of which is "szx"
# backend = DB, which has been defined
# test_backend = AerSimulator, for the fidelity check

num_qubits = 2
shots=100000
seed=117

# import numpy as np
# from qiskit import QuantumCircuit
from qiskit_experiments.library import ProcessTomography

qc_szx = QuantumCircuit(num_qubits)

for i in range(1, num_qubits):
    qc_szx.append(szx_gate, [0, i])

qptexp = ProcessTomography(qc_szx)
qptdata = qptexp.run(backend=backend, shots=shots, seed_simulator=seed).block_for_results()
choi_out = qptdata.analysis_results("state").value

import qiskit.quantum_info as qinfo
from qiskit.visualization import plot_state_city

# extracting a densitymatrix from choi_out

# 00

_rho_exp_00 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_00[i][j] = choi_out.data[i][j]

# experimental
rho_exp_00 = qinfo.DensityMatrix(_rho_exp_00)       

display(plot_state_city(rho_exp_00, title="rho_exp_00"))
print(f" The purity of the state rho_exp_00 is {rho_exp_00.purity()}.")

# ideal
qc_ideal_00 = QuantumCircuit(num_qubits)
for i in range(1, num_qubits):
    qc_ideal_00.rzx(np.pi / 2, 0, i)

rho_ideal_00 = qinfo.DensityMatrix.from_instruction(qc_ideal_00)

display(plot_state_city(rho_ideal_00, title="rho_ideal_00"))
print(f" The purity of the state rho_ideal_00 is {rho_ideal_00.purity()}.")

# fidelity
fidelity_00 = qinfo.state_fidelity(rho_exp_00, rho_ideal_00, validate=False)
print(f" The fidelity of rho_exp_00 is {fidelity_00}.")

# 01

_rho_exp_01 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_01[i][j] = choi_out.data[i+4][j+4]

# experimental
rho_exp_01 = qinfo.DensityMatrix(_rho_exp_01)

display(plot_state_city(rho_exp_01, title="rho_exp_01"))
print(f" The purity of the state is {rho_exp_01.purity()}.")

# ideal
qc_ideal_01 = QuantumCircuit(num_qubits)
qc_ideal_01.x(0)

for i in range(1, num_qubits):
    qc_ideal_01.rzx(np.pi / 2, 0, i)

rho_ideal_01 = qinfo.DensityMatrix.from_instruction(qc_ideal_01)

display(plot_state_city(rho_ideal_01, title="rho_ideal_01"))
print(f" The purity of the state rho_ideal_01 is {rho_ideal_01.purity()}.")

fidelity_01 = qinfo.state_fidelity(rho_exp_01, rho_ideal_01, validate=False)
print(f" The fidelity of the state rho_exp_01 is {fidelity_01}.")


# 10

_rho_exp_10 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_10[i][j] = choi_out.data[i+8][j+8]

# experimental
rho_exp_10 = qinfo.DensityMatrix(_rho_exp_10)

display(plot_state_city(rho_exp_10, title="rho_exp_10"))
print(f" The purity of the state is {rho_exp_10.purity()}.")

# ideal
qc_ideal_10 = QuantumCircuit(num_qubits)
qc_ideal_10.x(1)

for i in range(1, num_qubits):
    qc_ideal_10.rzx(np.pi / 2, 0, i)

rho_ideal_10 = qinfo.DensityMatrix.from_instruction(qc_ideal_10)

display(plot_state_city(rho_ideal_10, title="rho_ideal_10"))
print(f" The purity of the state rho_ideal_10 is {rho_ideal_10.purity()}.")

fidelity_10 = qinfo.state_fidelity(rho_exp_10, rho_ideal_10, validate=False)
print(f" The fidelity of the state rho_exp_10 is {fidelity_10}.")


# 11

_rho_exp_11 = np.array([[None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None],
                        [None, None, None, None]])

for i in range(4):
    for j in range(4):
        _rho_exp_11[i][j] = choi_out.data[i+12][j+12]

# experimental
rho_exp_11 = qinfo.DensityMatrix(_rho_exp_11)

display(plot_state_city(rho_exp_11, title="rho_exp_11"))
print(f" The purity of the state is {rho_exp_11.purity()}.")

# ideal
qc_ideal_11 = QuantumCircuit(num_qubits)
qc_ideal_11.x(0)
qc_ideal_11.x(1)

for i in range(1, num_qubits):
    qc_ideal_11.rzx(np.pi / 2, 0, i)

rho_ideal_11 = qinfo.DensityMatrix.from_instruction(qc_ideal_11)

display(plot_state_city(rho_ideal_11, title="rho_ideal_11"))
print(f" The purity of the state rho_ideal_11 is {rho_ideal_11.purity()}.")

fidelity_11 = qinfo.state_fidelity(rho_exp_11, rho_ideal_11, validate=False)
print(f" The fidelity of the state rho_exp_11 is {fidelity_11}.")
```
