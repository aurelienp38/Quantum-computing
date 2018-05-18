# Quantum Computing

<img align="right" src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/Complexity.jpg" width=450>
During the past decade, considerable progress has been achieved regarding the development of quantum computers, and a breakthrough in this field will have massive application particularily in research, cryptography and logistic. Google and IBM recently claimed the creation of a 72 and 50 qubit quantum chips respectively, making the possibility for a potential imminent quantum supremacy even more likely. [1]


&nbsp;

In May 2016, IBM launched Quantum Experience (QX), which enables anyone to easily connect to its 5qubit quantum processors via the IBM Cloud. (https://www.research.ibm.com/ibm-q/). Along with it's platform, IBM also developped `QISKit`, a Python library for the Quantum Experience API, where users can more easily apply quantum gates to run complex quantum algorithms and experiments.  

This repository is an introdution to quantum computing and contains the source code to run simples quantum algorithms. They are implemented with the python library `QISKit`, that can be easily installed with the command `$ pip install qiskit` (`Python 3.5` is required). More information is available at https://qiskit.org/.


&nbsp;

## Quantum Gates

<img align="left" src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/gate.png" width=170>
In analogy with the classical gates NOT, AND, OR, ... that are the building blocks for classical circuits, there are quantum gates that perform basic operations on qubits. The most common quantum gates are summarized here --> https://en.wikipedia.org/wiki/Quantum_logic_gate. 

For example, the Hadamard gate, H, performs the following operartion:
<img align="left" src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/hadamar.png" width=550>



&nbsp;

&nbsp;


### Creating and measuring a Bell state
The following code, available in the `src` folder, create a Bell state and measure it 1000 times. 

<img src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/Bell.png" width=180>

```python
from qiskit import QuantumProgram
qp = QuantumProgram()
qr = qp.create_quantum_register('qr',2)      #Initialize 2 qubits to perform operations
cr = qp.create_classical_register('qc',2)    #Initialize 2 classical bits to store the measurements
qc = qp.create_circuit('Bell',[qr],[cr])
qc.h(qr[0])                                  #Apply Hadamar gate
qc.cx(qr[0], qr[1])                          #Apply CNOT gate
qc.measure(qr[0], cr[0])                     #Measure qubit 0 and store the result in bit 0
qc.measure(qr[1], cr[1])                     #Measure qubit 1 and store the result in bit 1

result = qp.execute('Bell', shots=1000)      #Compile and run the Quantum Program 1000 times
print(result.get_counts('Bell'))
```
A possible result that we get is
```python
{'11': 494, '00': 506}
```




&nbsp;


## Shor's Algorithm

The [Shor's algorithm](https://en.wikipedia.org/wiki/Shor%27s_algorithm), proposed by Peter Shor in 1995 [2], is today one of the most famous quantum algorithm; it is considerably significant because it enables to find 2 factors of a number in polynomial time with its number of digits, while the security of our online transactions rests on the assumption that factoring integers with a thousand or more digits is practically impossible. Shor's algorithm was first experimentally demonstrated in 2001 by a group at IBM, which factored 15 into 3 and 5, using a quantum computer of 7 qubits [3].


&nbsp;

### Complexity of factoring

<img align="right" src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/complexity.png" width=510>
Let N be the number to be factorized, and d~log(N) its number of digit. Then the most efficient classical factoring algorithm currently known is the [General number field sieve](https://en.wikipedia.org/wiki/General_number_field_sieve), which has an exponential asymptotic runtime to the number of digits : O(exp(d^1/3)). On the other hand, Shor’s factoring algorithm has an asymptotic runtime polynomial in d : O(d^3). 

This remarquable difference between polynomial and exponential runtime scaling currently places the Factoring problem into the [BQP\P](https://en.wikipedia.org/wiki/BQP) decision class (cf. figure in introduction).


&nbsp;

### The Algorithm

#### Preambule

We want to find two factors *p1* and *p2* that divide N. Before diving into the algorithm, we have to make sure that:
⋅⋅* N is odd (if it's even, then 2 is a trivial factor)
⋅⋅* N is not the power of a prime

#### Classical part

With some Arithmetic, Group theory, Euler's Theorem and Bézout's identity, it is possible to reduce the factorization problem into a period finding problem of the modular exponential function (see for more details). The classical part of the algorithm is implemented as follow:
```python
def Shor(N):
    while True:
    
        #1) pick a random number a<N
        a = randint(1, N-1)    
    
        #2) check for the GCD(a,N)
        p = gcd(a,N)
        if p != 1:  # We found a nontrivial factor
            p1 = p
            p2 = N/p
            break
        
        #3) Compute the periode r, if r is a goof period, we found prime factors
        else:            
            
            r = quantum_period(a,N)
        
            if r % 2 == 0 :
                if a**(r/2) % N != -1:
                    p1 = gcd(a**(r/2)-1,N)
                    p2 = gcd(a**(r/2)+1,N)
                
    return p1, p2
```


#### Period finding

##### Modular Exponentiation


##### Controlled gates

<img src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/Shor.png" width=800>

More complete description available on the [IBM User Guide](https://quantumexperience.ng.bluemix.net/proxy/tutorial/full-user-guide/004-Quantum_Algorithms/110-Shor's_algorithm.html).


&nbsp;

### Simplified implementation

<img src="https://raw.githubusercontent.com/Aurelien-Pelissier/IBMQ-Quantum-Computing/master/img/modulation.png" width=500>



&nbsp;

## References

[1]: Kelly, J. (2018). Engineering superconducting qubit arrays for Quantum Supremacy. Bulletin of the American Physical Society. [http://meetings.aps.org/Meeting/MAR18/Session/A33.1]


[2]: Shor, P. W. (1999). Polynomial-time algorithms for prime factorization and discrete logarithms on a quantum computer. SIAM review, 41(2), 303-332. [https://arxiv.org/abs/quant-ph/9508027]


[3]: Vandersypen, L. M., Steffen, M., Breyta, G., Yannoni, C. S., Sherwood, M. H., & Chuang, I. L. (2001). Experimental realization of Shor's quantum factoring algorithm using nuclear magnetic resonance. Nature, 414(6866), 883. [https://www.nature.com/articles/414883a]


[4]: Markov, I. L., & Saeedi, M. (2012). Constant-optimized quantum circuits for modular multiplication and exponentiation. arXiv preprint arXiv:1202.6614. [https://arxiv.org/abs/1202.6614]
