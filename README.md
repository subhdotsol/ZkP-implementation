

# **Zero-Knowledge Proof: Sum & Product Verification using Circom + SnarkJS**

This project demonstrates a simple Zero-Knowledge Proof (ZKP) circuit written in **Circom**, which verifies:

* The **sum** of two private inputs
* The **product** of the same private inputs

…without revealing the inputs themselves.

Perfect introduction for beginners learning **snarkjs**, **Circom**, and **ZKP workflows**.

---

# 🚀 **1. Key ZKP Concepts**

### **Proof**

A proof allows a prover (Bob) to convince a verifier (Alice) that he knows some secret information **without revealing the secret**.

### **Witness**

The hidden inputs used to generate the proof.
Example: Bob’s actual password, age, number, etc.

### **Circuit**

The logical computation performed on inputs.
In Circom, circuits define constraints like:

* `sum = a + b`
* `product = a * b`

These constraints must be satisfied for the proof to be valid.

### **Verifier**

The party (Alice) who checks the proof without ever seeing the inputs.

### **Prover**

The person who has the secret data (Bob) and generates the proof.

---

# 🔧 **2. Circuit (Using Circom)**

A **Circom circuit** defines the computation that should be proven in zero knowledge.

Circom is a domain-specific language that lets you:

* Define computations
* Add constraints
* Generate R1CS / WASM files
* Produce proofs using snarkjs

### Example Use-Cases

* Prove you are older than 18 without revealing age
* Prove you know a password without revealing it
* Prove a value is within a range
* Prove computations over encrypted data

---

# 🧠 **3. R1CS (Rank-1 Constraint System)**

Circom compiles circuits into **R1CS**, the mathematical representation of constraints.

* File Type: `.r1cs`
* Structure: **Left * Right = Output**
* Purpose: Convert your circuit into a format cryptographic systems understand.

R1CS is required for generating:

* Witness
* Proof
* Verifying key

---

# 🧩 **4. Circuit Used in This Project**

```circom
pragma circom 2.0.0;

template SumProduct(){

    // Inputs
    signal input a;
    signal input b;

    // Outputs
    signal output sum;
    signal output product;

    // Constraints 
    sum <== a + b;
    product <== a * b;
}

component main = SumProduct();
```

This circuit proves *"I know two numbers a & b such that these sum/product outputs are correct"*.

---

# 🛠️ **5. Installation**

### Install Circom

```
git clone https://github.com/iden3/circom.git
cd circom
cargo build --release
export PATH="$PATH:$(pwd)/target/release"
```

### Install snarkjs

```
npm install -g snarkjs
```

(or)

```
bun add -g snarkjs
```

---

# 🧪 **6. Compile the Circuit**

```
circom sumproduct.circom --r1cs --wasm --sym
```

This generates:

* `sumproduct.r1cs`
* `sumproduct.wasm`
* `sumproduct.sym`

---

# 🧩 **7. Create the Witness**

Create an `input.json`:

```json
{
  "a": 4,
  "b": 7
}
```

Generate witness:

```
node sumproduct_js/generate_witness.js sumproduct_js/sumproduct.wasm input.json witness.wtns
```

---

# 🔐 **8. Setup Trusted Ceremony (Powers of Tau)**

```
snarkjs powersoftau new bn128 12 pot12_0000.ptau
snarkjs powersoftau contribute pot12_0000.ptau pot12_0001.ptau
snarkjs groth16 setup sumproduct.r1cs pot12_0001.ptau circuit_final.zkey
```

Export verification key:

```
snarkjs zkey export verificationkey circuit_final.zkey verification_key.json
```

---

# 🧾 **9. Generate Proof**

```
snarkjs groth16 prove circuit_final.zkey witness.wtns proof.json public.json
```

---

# 🛡️ **10. Verify Proof**

```
snarkjs groth16 verify verification_key.json public.json proof.json
```

If everything is correct, you get:

```
OK
```

---

# 🌐 Optional: Generate Verifier Smart Contract

```
snarkjs zkey export solidityverifier circuit_final.zkey verifier.sol
```

---

# 🎯 **Conclusion**

You now have:

* A working Circom circuit
* Proof generation
* Proof verification
* A complete ZKP pipeline

This Repo gives beginners all the theory + hands-on steps needed to understand how ZKPs work using snarkjs and Circom.

