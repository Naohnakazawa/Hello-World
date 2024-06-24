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

### test module 2
```
# test module
# Get eigenvalues of your hamiltonian
# Use scipy.linalg.eigvals

def get_eigvals_hamiltonian(hamil):
    """Get eigenvalues of your hamiltonain"""
    
    import numpy as np
    from scipy import linalg
    
    a = hamil.to_matrix()
    b = linalg.eigvals(a)
    
    return b

pauli_op = SparsePauliOp(['ZII', 'IZI', 'IIZ', 'XII', 'IXI', 'IIX'])
get_eigvals_hamiltonian(hamil=pauli_op)
```


array([ 4.24264069+0.j,  1.41421356+0.j, -4.24264069+0.j, -1.41421356+0.j, -1.41421356+0.j,  1.41421356+0.j, -1.41421356+0.j,  1.41421356+0.j])




### AerSimulator() vs FakeScherbrooke()
```
pauli_op = SparsePauliOp(['ZII', 'IZI', 'IIZ', 'XII', 'IXI', 'IIX'])
```






![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/5d2200e5-d901-44b8-950a-d4cace8578bb)







![image](https://github.com/Naohnakazawa/Hello-World/assets/117684192/6ed54b02-dc18-48b5-9d5e-a478685d0397)



