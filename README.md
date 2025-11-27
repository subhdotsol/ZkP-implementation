
# 🔐 ZK Proof Demo — Circom + SnarkJS (Interactive Guide)

This project demonstrates a simple Zero-Knowledge Proof (ZKP) circuit written in **Circom**, which verifies:

* The **sum** of two private inputs
* The **product** of the same private inputs

…without revealing the inputs themselves.

Perfect introduction for beginners learning **snarkjs**, **Circom**, and **ZKP workflows**.

We'll compile the circuit → generate witnesses → run trusted setup → generate proof → verify it → (optional) export to Solidity.

---

#  Project Setup (Folder Structure)

Create these 3 folders in your project root:

```
/circuits
/inputs
/outputs
```

---

#  Step 1 — Write Your Circuit

Create the file:

```
circuits/sumproduct.circom
```

Put your circuit logic inside it.

---

#  Step 2 — Compile Circuit (Generate R1CS, WASM, SYM)

Run this command:

```sh
circom circuits/sumproduct.circom --r1cs --wasm --sym -o outputs
```

This generates:

* `sumproduct.r1cs` → circuit constraints
* `sumproduct.wasm` → witness generator
* `.sym` → plaintext constraint symbols
* `sumproduct_js/` → JS wrapper for witness generation

---

#  Step 3 — Create Input File

Inside `inputs/`, create:

```
input.json
```

Add:

```json
{
  "a": "7",
  "b": "5"
}
```

---

#  Step 4 — Generate the Witness

Run the witness generator JS script:

```sh
node outputs/sumproduct_js/generate_witness.js \
     outputs/sumproduct_js/sumproduct.wasm \
     inputs/input.json \
     witness.wtns
```

This will output:

```
witness.wtns
```

This file represents the computed values of the circuit for your input.

---

#  Step 5 — Trusted Setup (Powers of Tau + Groth16)

### Phase 1 — Powers of Tau

```sh
snarkjs powersoftau new bn128 12 pot12_0000.ptau
```

Contribute entropy:

```sh
snarkjs powersoftau contribute pot12_0000.ptau pot12_0001.ptau
```

If you hit an error like "TAU not prepared", run:

```sh
snarkjs powersoftau prepare phase2 pot12_0001.ptau pot12_final.ptau
```

### Phase 2 — Verify Powers of Tau

```sh
snarkjs powersoftau verify pot12_final.ptau
```

---

#  Step 6 — Generate Groth16 Setup

Create the proving key + verifying key:

```sh
snarkjs groth16 setup outputs/sumproduct.r1cs pot12_final.ptau circuit_0000.zkey
```

Export the verification key:

```sh
snarkjs zkey export verificationkey circuit_0000.zkey verification_key.json
```

---

#  Step 7 — Generate the Zero-Knowledge Proof

```sh
snarkjs groth16 prove circuit_0000.zkey witness.wtns proof.json public.json
```

Outputs:

* `proof.json` → the ZK-proof
* `public.json` → the public inputs/outputs

---

#  Step 8 — Verify the Proof

```sh
snarkjs groth16 verify verification_key.json public.json proof.json
```

If everything is correct, you should see:

```
OK!
```

---

#  Step 9 — (Optional) Generate Solidity Verifier Contract

Run:

```sh
snarkjs zkey export solidityverifier circuit_0000.zkey verifier.sol
```

Deploy `verifier.sol` in your smart contract project.

---

# 🎉 Yay!!! You're Done!

You've now:

* Written a Circom circuit
* Compiled it
* Generated inputs
* Created a witness
* Completed Powers of Tau
* Ran Groth16 setup
* Created a proof
* Verified it


