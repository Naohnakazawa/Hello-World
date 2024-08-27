### QubitSpectroscopy (backend=MockIQBackend, this may be something wrong!)
```
# backend MockIQBackend
from qiskit_experiments.framework import BackendData
from qiskit_experiments.test.mock_iq_backend import MockIQBackend
from qiskit_experiments.test.mock_iq_helpers import MockIQSpectroscopyHelper

exp_helper=MockIQSpectroscopyHelper(gate_name="Spec", freq_offset=0.0, line_width=2.0e6)
backend=MockIQBackend(exp_helper)

# mwc
import numpy as np
from qiskit_experiments.library.characterization import QubitSpectroscopy

qubit=1 # Note qubit=1 !
exp_helper_dt=BackendData(backend).dt
exp_helper_freq= BackendData(backend).drive_freqs[qubit]

frequencies_0 = list(np.linspace(exp_helper_freq - 20.e6, exp_helper_freq + 20.e6, 51))
frequencies_1 = list(np.linspace(exp_helper_freq - 2.e6, exp_helper_freq + 2.e6, 51))
# frequencies = frequencies_0 + frequencies_1
frequencies = [4999968006.92, 5000768006.92, 5001568006.92, 5002368006.92, 5003168006.92, 5003968006.92, 5004768006.92, 5005568006.92, 5006368006.92, 5007168006.92, 5007968006.92, 5008768006.92, 5009568006.92, 5010368006.92, 5011168006.92, 5011968006.92, 5012768006.92, 5013568006.92, 5014368006.92, 5015168006.92, 5015968006.92, 5016768006.92, 5017568006.92, 5017968006.92, 5018048006.92, 5018128006.92, 5018208006.92, 5018288006.92, 5018368006.92, 5018448006.92, 5018528006.92, 5018608006.92, 5018688006.92, 5018768006.92, 5018848006.92, 5018928006.92, 5019008006.92, 5019088006.92, 5019168006.92, 5019248006.92, 5019328006.92, 5019408006.92, 5019488006.92, 5019568006.92, 5019648006.92, 5019728006.92, 5019808006.92, 5019888006.92, 5019968006.92, 5020048006.92, 5020128006.92, 5020208006.92, 5020288006.92, 5020368006.92, 5020448006.92, 5020528006.92, 5020608006.92, 5020688006.92, 5020768006.92, 5020848006.92, 5020928006.92, 5021008006.92, 5021088006.92, 5021168006.92, 5021248006.92, 5021328006.92, 5021408006.92, 5021488006.92, 5021568006.92, 5021648006.92, 5021728006.92, 5021808006.92, 5021888006.92, 5021968006.92, 5022368006.92, 5023168006.92, 5023968006.92, 5024768006.92, 5025568006.92, 5026368006.92, 5027168006.92, 5027968006.92, 5028768006.92, 5029568006.92, 5030368006.92, 5031168006.92, 5031968006.92, 5032768006.92, 5033568006.92, 5034368006.92, 5035168006.92, 5035968006.92, 5036768006.92, 5037568006.92, 5038368006.92, 5039168006.92, 5039968006.92]
exp = QubitSpectroscopy(physical_qubits = (qubit,),
                        frequencies = frequencies, 
                        backend = backend, 
                        absolute = True, )
exp.set_experiment_options(amp=1)  # This is not a default value

exp_data = exp.run().block_for_results()
exp_data.figure(0)
```


![image](https://github.com/Naohnakazawa/Plasmid-Box/assets/117684192/5f306cd4-2979-4efa-973a-198b5187bdce)
