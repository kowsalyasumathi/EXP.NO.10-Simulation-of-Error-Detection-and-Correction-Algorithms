# EXP.NO.10-Simulation-of-Error-Detection-and-Correction-Algorithms
10.Simulation of Error Detecion and Correction Algorithms

# AIM
To simulate and analyze the working of error detection and correction algorithms , including Parity Check using  Hamming Code to ensure reliable data transmission.

# SOFTWARE REQUIRED
Google Colab (python)

# ALGORITHMS
```
1)Select a data block (binary string) to be transmitted.

2)Choose one or more algorithms for simulation:

3)Parity Bit

4)Checksum

5)Hamming Code

6)Apply the algorithm to encode the data.

7)Simulate transmission errors (by flipping bits).

8)Apply the detection or correction method at the receiver.

9)Display whether an error was detected or corrected.

10)Compare results of different algorithms.
```
# PROGRAM
```
import numpy as np

pb = []           # Parity matrix rows
Ik = []           # Identity matrix
p = []
m = []
h_dis = []
r_code = []
err = []

# Input for matrix dimensions
col = int(input("Enter the number of parity bits: "))
row = int(input("Enter the number of message bits: "))

# Input the parity matrix (P)
print("\nEnter the parity matrix rows:")
for i in range(row):
    p = list(map(int, input(f"Row {i+1}: ").split()))
    if len(p) != col:
        raise ValueError(f"Each row must have {col} elements.")
    pb.append(p)

# Convert to numpy array
p_mat = np.array(pb, dtype=int)
Ik = np.eye(row, dtype=int)

# Generator Matrix G = [I | P]
g_mat = np.hstack((Ik, p_mat))

# Codeword length n and message bits k
k = g_mat.shape[0]
n = g_mat.shape[1]

# Generate all possible message vectors (2^k)
m = np.array([[1 if (i >> (k - j - 1)) & 1 else 0 for j in range(k)] for i in range(2 ** k)])

# Generate codewords: c = m * G mod 2
c = np.mod(np.dot(m, g_mat), 2)

# Hamming weights
for i, row in enumerate(c):
    h_dis1 = np.sum(row)
    h_dis.append(h_dis1)
h_mat = np.array(h_dis).reshape(-1, 1)
d_min = np.min(h_dis[1:])

# Parity-check matrix H = [P^T | I]
p_t = p_mat.T
h_check = np.hstack((p_t, np.eye(col, dtype=int)))
ht = h_check.T  # Transpose of H

# Output Generator Matrix
print("\n**********")
print("Generator Matrix [G = I | P]:")
for row in g_mat:
    print(" ".join(map(str, row)))

# Output Codewords
print("\n**********")
print("Message Bits\tCodeword\tHamming Weight")
for i in range(len(m)):
    msg_str = " ".join(map(str, m[i]))
    code_str = " ".join(map(str, c[i]))
    print(f"{msg_str}\t{code_str}\t\t{h_dis[i]}")

# Minimum Hamming distance
print("\n**********")
print(f"Minimum Hamming Distance: {d_min}")

# Output Parity Check Matrix
print("\n**********")
print("Parity Check Matrix [H = P^T | I]:")
for row in h_check:
    print(" ".join(map(str, row)))

# Output Transpose of Parity Check Matrix
print("\n**********")
print("Transpose of Parity Check Matrix (H^T):")
for row in ht:
    print(" ".join(map(str, row)))

# Receive codeword
rc = list(map(int, input("\nEnter the received codeword: ").split()))
if len(rc) != n:
    raise ValueError("Received codeword length must match codeword length n.")
r_c = np.array([rc])

# Syndrome calculation: s = r * H^T mod 2
e = np.mod(np.dot(r_c, ht), 2).flatten()

# Find error position
err = np.zeros(n, dtype=int)
for i in range(n):
    if np.array_equal(e, ht[i]):
        err[i] = 1
        break

print("\n**********")
print("Syndrome:", " ".join(map(str, e)))
print("Error vector:", " ".join(map(str, err)))

# Correct the error
corrected = (r_c.flatten() + err) % 2
print("Corrected Codeword:", " ".join(map(str, corrected)))

# Optional: Syndrome Table (first few entries)
print("\n**********")
print("Syndrome Matrix:")
for i in range(n):
    s = ht[i]
    ev = np.eye(n, dtype=int)[i]
    print(f"{' '.join(map(str, s))}  {' '.join(map(str, ev))}")

print("**********")
```
# OUTPUT
```
Enter the parity matrix rows:

**********
Generator Matrix [G = I | P]:
1 0 0 0 1 1 0 1
0 1 0 0 1 0 0 1
0 0 1 0 1 1 1 0
0 0 0 1 1 0 1 0

**********
Message Bits	Codeword	Hamming Weight
0 0 0 0	0 0 0 0 0 0 0 0		0
0 0 0 1	0 0 0 1 1 0 1 0		3
0 0 1 0	0 0 1 0 1 1 1 0		4
0 0 1 1	0 0 1 1 0 1 0 0		3
0 1 0 0	0 1 0 0 1 0 0 1		3
0 1 0 1	0 1 0 1 0 0 1 1		4
0 1 1 0	0 1 1 0 0 1 1 1		5
0 1 1 1	0 1 1 1 1 1 0 1		6
1 0 0 0	1 0 0 0 1 1 0 1		4
1 0 0 1	1 0 0 1 0 1 1 1		5
1 0 1 0	1 0 1 0 0 0 1 1		4
1 0 1 1	1 0 1 1 1 0 0 1		5
1 1 0 0	1 1 0 0 0 1 0 0		3
1 1 0 1	1 1 0 1 1 1 1 0		6
1 1 1 0	1 1 1 0 1 0 1 0		5
1 1 1 1	1 1 1 1 0 0 0 0		4

**********
Minimum Hamming Distance: 3

**********
Parity Check Matrix [H = P^T | I]:
1 1 1 1 1 0 0 0
1 0 1 0 0 1 0 0
0 0 1 1 0 0 1 0
1 1 0 0 0 0 0 1

**********
Transpose of Parity Check Matrix (H^T):
1 1 0 1
1 0 0 1
1 1 1 0
1 0 1 0
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1

Enter the received codeword: 1 1 1 1 0 0 0 0 

**********
Syndrome: 0 0 0 0
Error vector: 0 0 0 0 0 0 0 0
Corrected Codeword: 1 1 1 1 0 0 0 0

**********
Syndrome Matrix:
1 1 0 1  1 0 0 0 0 0 0 0
1 0 0 1  0 1 0 0 0 0 0 0
1 1 1 0  0 0 1 0 0 0 0 0
1 0 1 0  0 0 0 1 0 0 0 0
1 0 0 0  0 0 0 0 1 0 0 0
0 1 0 0  0 0 0 0 0 1 0 0
0 0 1 0  0 0 0 0 0 0 1 0
0 0 0 1  0 0 0 0 0 0 0 1
**********
```
 
# RESULT / CONCLUSIONS
Thus the simulation of Error detection and correction is successfully verified using Hamming code.
