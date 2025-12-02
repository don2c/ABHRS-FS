# ABHRS: Attribute-Based Hiding Ring Signatures with Forward Security

This repository contains an executable R prototype of the ABHRS / ABHRS-FS model.  
It follows the algorithmic structure of the research paper and provides a small, self-contained mock implementation for testing ideas and demonstrating workflow.  


## Introduction

Modern distributed systems such as Online Social Networks (OSNs) and e-health platforms require access control that protects privacy while still supporting accountability and post-quantum resilience. Classical ring signatures, group signatures, and attribute-based signatures only cover parts of this requirement.

ABHRS (Attribute-Based Hiding Ring Signatures) and its forward-secure variant ABHRS-FS aim to model a setting where:

- Attributes and policies remain hidden during verification  
- Signer ambiguity is preserved under sparse and mixed rings  
- Keys evolve over epochs to protect past signatures after compromise  
- Accountability is still possible under controlled opening

This R code offers a research-oriented mock of these concepts for experimentation and teaching.

---

## Background

Existing ring and attribute-based schemes often leak attribute or policy structure, assume static rings, or ignore key evolution. Many post-quantum proposals focus on correctness and efficiency but do not model accountable opening or parameter tuning under realistic workloads.

The prototype in this repository mirrors the logical design of ABHRS / ABHRS-FS:

- Credential issuance bound to attributes  
- Certificate validation with a trust anchor  
- Ring formation with decoys to reduce sparse-ring leakage  
- Epoch-based key evolution  
- Attribute-oblivious verification through a proof layer  
- A simple neural adversarial co-design loop that treats the scheme as a black box and adjusts parameters such as ring size

All “crypto” in this script is symbolic. It only preserves the structure of algorithms, not the security of real lattice primitives.

---

## Key Features

- **ABHRS / ABHRS-FS Workflow**  
  Implements the main stages described in the paper: credential issuance, certificate validation, ring formation, signing at epoch *t*, and verification.

- **Ring-Indistinguishable Padding (RIP)**  
  Builds rings that mix honest and decoy keys, then randomises order to model signer ambiguity under sparse and mixed rings.

- **Epoch Key Hardening (EKH)**  
  Uses a simple key-update function to represent forward security in the code path and to keep the interface close to the theoretical construction.

- **Attribute-Oblivious Verification (AOV) and PCP Stub**  
  Uses a “PCP proof” placeholder to mimic zero-knowledge enforcement of attribute and policy predicates without revealing internal values.

- **Neural Adversarial Co-Design Loop (Mock)**  
  Includes a simple loop that simulates transcripts, assigns toy leakage scores, and increases ring size within a cost budget.  
  This mirrors how neural attackers could guide parameter selection offline.

- **Executable Demonstration**  
  Running the script builds a small OSN-like setting with CAs, users, policies, rings, signatures, verification, and a toy parameter tuning routine.

---

## Our Solution (Prototype View)

The prototype translates the ABHRS / ABHRS-FS design into an executable R script that:

1. Creates a small PKI-like environment with two certification authorities and three users with attributes.
2. Issues an attribute credential to a user and binds it to a simple certificate chain.
3. Forms a ring that combines honest keys and decoys under a target size.
4. Generates a signature at a chosen epoch using a key-update function, an abstract proof system, and a ring signature stub.
5. Verifies the signature by checking the commitment structure, the proof, and the ring signature.
6. Runs a parameter tuning loop that simulates neural adversaries by assigning lower leakage scores to larger rings within a cost limit.

The goal is structural fidelity. The script mirrors the algorithmic flow so that reviewers and students can see how the pieces interact, even though the cryptographic layer is only symbolic.

---

## Benefits

Although only a mock, the prototype offers several research and teaching benefits.

- **Clarifies System Flow**  
  Shows how credential issuance, CA chains, attribute policies, ring padding, and epoch keys fit together in one pipeline.

- **Supports Rapid Experimentation**  
  Allows quick changes to policy functions, ring sizes, and toy leakage or cost models for conceptual testing.

- **Aids Paper Review and Reproducibility**  
  Serves as an executable reference for the algorithms described in the ABHRS / ABHRS-FS draft, which can help reviewers understand design choices.

- **Teaches Architecture of Post-Quantum Access Control**  
  Provides a clean, readable example of how to organise a system that targets privacy, accountability, and quantum-resistant primitives at the design level.

---

## Algorithms Implemented

The R script contains direct mock counterparts of the key algorithms in the paper.

### 1. Credential Issuance (ABHRS / ABHRS-FS)

Functions  
- `issue_credential()`  
- `credential_issuance()`

Purpose  
- Issue a credential `sigma_u` for user attributes `A_u` under CA secret key `SK_CA`.  
- Store the credential for later use inside the proof witness.

Role  
- Models attribute binding and CA authority before ring signing.

---

### 2. Embedded Certificate Validation Predicate

Functions  
- `build_mock_chain()`  
- `merkle_verify()`  
- `sig_verify()`  
- `cl_verify()`  
- `valid_cert_chain_predicate()`

Purpose  
- Build a simple certificate chain from user to root CA.  
- Check that the chain is valid, the root belongs to the trusted set `h_CA`, the attributes satisfy the policy, and the epoch key matches the commitment.

Role  
- Represents the inner predicate that a PCP proof would enforce in the real construction.

---

### 3. Ring Formation with Ring-Indistinguishable Padding (RIP)

Functions  
- `sample_decoy_keys()`  
- `rip_gen_ring()`

Purpose  
- Start from a list of honest keys `H`.  
- Add decoy keys up to a target ring size.  
- Randomly permute the final ring.

Role  
- Models ring padding and anonymisation across mixed and sparse rings.

---

### 4. Signing at Epoch *t* (ABHRS-FS)

Functions  
- `key_update()`  
- `sign_epoch()`

Purpose  
- Evolve a user key from `SK_prev` to `SK_t`.  
- Commit to the epoch key.  
- Build a PCP statement and witness.  
- Produce a proof with `pcp_prove()`.  
- Generate a ring signature with `ring_sign()`.

Role  
- Mirrors the ABHRS-FS signing interface and data flow, including AOV, EKH, RIP, and PCP hooks.

---

### 5. Verification (ABHRS / ABHRS-FS)

Functions  
- `valid_key_public_only()`  
- `verify_ABHRS_signature()`

Purpose  
- Reconstruct the public statement from the signature.  
- Check a basic commitment structure.  
- Verify the PCP proof.  
- Verify the ring signature.

Role  
- Represents the verifier’s view and conditions for acceptance.

---

### 6. Neural Adversarial Co-Design (Offline)

Functions  
- `simulate_ABHRS_FS()`  
- `cost_model()`  
- `optimise_theta()`  
- `neural_adversarial_codesign()`

Purpose  
- Generate synthetic transcripts under current parameters.  
- Assign toy leakage scores that fall as ring size increases.  
- Compare leakage against a simple cost model based on ring size.  
- Update `theta` within a cost budget.

Role  
- Encodes the structure of a neural co-design loop that uses offline adversaries to tune parameters for ABHRS-FS.

---

## Getting Started

These steps describe how to set up and run the prototype locally.

### Prerequisites

- R version 4.0 or newer  
- Optional but recommended  
  - RStudio for a more comfortable development environment  
- Basic familiarity with running R scripts

All used packages are part of base R:

- `stats`  
- `utils`  
- `methods`

No external CRAN packages are required.

---

### Installation

1. Clone the repository

```bash
git clone https://github.com/your-username/ABHRS_R_Prototype.git
cd ABHRS_R_Prototype


### Open the main script

The repository should contain a main script, for example:

abh_rs_core.R

Open this file in RStudio or any R console.

###Running the Project

From an R console (inside the project folder):
On completion you should see output similar to:

Round 1 target_ring_size = 4 L = ...
Round 2 target_ring_size = 5 L = ...
...
Credential chain check for u1: TRUE
Verification result for Sigma_u1_t1: TRUE
Tuned parameters after neural co design:
$target_ring_size
[1] ...
$decoy_ratio
[1] 0.5

This indicates that:

*The certificate validation predicate succeeded for user u1.

*The ABHRS-style signature was verified correctly.

*The neural co-design loop ran for several rounds and produced a tuned parameter vector.

### Testing and Extending
#Change the access policy:

You can run small experiments by editing the script and re-sourcing it.

Examples

policy_nurse <- function(Au) {
  isTRUE(Au$role == "nurse")
}

#Modify the initial parameter vector:

theta_star <- list(
  target_ring_size = 6,
  decoy_ratio      = 0.6
)

# Add new messages and workloads:
workload_distribution$messages <- c(
  "read_record",
  "update_record",
  "sign_note",
  "export_summary"
)

### Contributing

Contributions that improve clarity, documentation, or experimental structure are welcome.
Suggestions include:

1. Cleaner separation of configuration and core logic

2. Richer policy examples for OSNs and e-health scenarios

3. Stronger instrumentation for analysing toy leakage and cost metrics

4. Hooks for replacing the placeholders with real cryptographic libraries in other languages

If you open a pull request, please:

1. Keep the code readable and comment major steps

2. Preserve the distinction between mock crypto and any real primitives

3. Avoid adding external dependencies unless they clearly support analysis
