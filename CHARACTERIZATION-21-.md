# Characterization experiments 21, 22, 23
### CRH
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")

initial_layout = list(range(127))

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import CrossResonanceHamiltonian

qubits=(17, 30)
exp=CrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)
exp.set_experiment_options(max_circuits=None, 
                       flat_top_widths=None, 
                       durations=None, 
                       min_durations=1.0e-07, 
                       max_durations=1.2e-06, 
                       num_durations=48, 
                       amp=0.2024212826036257, 
                       amp_t=0.0, 
                       sigma=30, 
                       risefall=2)

exp.circuits()[287].draw("mpl")

# run carefully on real devices!
# exp_data=exp.run()
```




![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/35f11f7c-9fe4-46d0-9a64-c4126e0485e5)





### ECRH
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend 
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_kyoto")

initial_layout = list(range(127))

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization import EchoedCrossResonanceHamiltonian

qubits=(17, 30)
exp=EchoedCrossResonanceHamiltonian(physical_qubits=qubits, backend=backend)

exp.set_experiment_options(max_circuits=None, 
                       flat_top_widths=None, 
                       durations=None, 
                       min_durations=1.0e-07, 
                       max_durations=1.2e-06, 
                       num_durations=48, 
                       amp=0.2024212826036257, 
                       amp_t=0.0, 
                       sigma=30, 
                       risefall=2)

exp.circuits()[287].draw("mpl")

# run carefully on real devices!
# exp_data=exp.run()
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/a547ff5d-481b-4cd0-8aa3-1d3565ae031d)






### StarkP1Spectroscopy
```
# section: example
# ..jupyter-execute::
# :hide-code:

# backend ibm_brisbane
from qiskit_ibm_provider import IBMProvider

INSTANCE="ibm-q/open/main"
provider = IBMProvider(instance=INSTANCE)
backend = provider.get_backend("ibm_brisbane")
initial_layout = list(range(127))

# ..jupyter-execute::

# mwc
from qiskit_experiments.library.characterization.t1 import StarkP1Spectroscopy

qubit=13
exp = StarkP1Spectroscopy((qubit,), backend=backend)
exp.set_run_options(shots=100)
exp_data = exp.run().block_for_results()
result=exp_data.analysis_results()

exp_data.figure(0)
```





![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/752fc54e-5244-4ac9-927b-a3ba9dc72c09)





