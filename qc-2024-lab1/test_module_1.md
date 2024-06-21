### test module 1 
```
# test module 
# Statevector

qc = QuantumCircuit(3) #your_code_here

# Step 2 (provided)
qc.ry(1.91063324, 0)

# Add steps 3-6 below
qc.ch(0,1)
qc.cx(1,2)
qc.cx(0,1)
qc.x(0)

def state_check(circ):
    """Test your state """
    
    import numpy as numpy
    import qiskit.quantum_info as qi
    
    state = qi.Statevector(circ).data
    
    return state

state_check(qc)
```
