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




