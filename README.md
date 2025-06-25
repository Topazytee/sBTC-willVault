

# sBTC-WillVault: Automated Decentralized Will Execution Contract

A Clarity smart contract for **automated inheritance**, **time-locked fund release**, **multi-oracle death confirmation**, **NFT inheritance**, and **dispute resolution**. Built on Bitcoin via the Stacks blockchain, it ensures secure, trustless distribution of digital assets upon death.

---

## 📜 Features

* **Multi-oracle verification** for death confirmation (decentralized control)
* **Time-locked inheritance** to enforce delay in claims
* **NFT ownership transfer** to beneficiaries
* **On-chain will hash registry** for audit/reference
* **Phased inheritance release** for structured payouts
* **Dispute resolution mechanism** with community voting
* **Emergency controls** by contract owner

---

## 🧠 Contract Components

### ✅ Initialization

```clojure
(initialize-contract (oracle-list (list 5 principal)))
```

* Sets trusted oracles for verifying death
* Can only be called by the `contract-owner`

---

### 👥 Beneficiaries

```clojure
(add-beneficiary beneficiary share lock-period nft-list)
```

* Assigns:

  * `share` (uint: percentage of total)
  * `lock-period` (in blocks after which claim is allowed)
  * `nft-list`: List of NFT token IDs

---

### ⚰️ Death Confirmation by Oracles

```clojure
(confirm-death)
```

* Each oracle calls once
* When confirmations ≥ `required-confirmations`, death is marked as confirmed

---

### 🧾 Will Hash Registry

```clojure
(update-will-hash new-hash)
```

* Updates the document hash of the user's will
* Hash must be 32 bytes (e.g., SHA256)

---

### 💰 Claiming Inheritance

```clojure
(claim-inheritance)
```

* Can only be executed if:

  * Death is confirmed
  * Beneficiary's time-lock has expired
  * Not already claimed
* Transfers:

  * Fungible token share (minus 2% tax)
  * Allocated NFTs

---

### 🧬 NFT Ownership Transfer

Handled by:

```clojure
(transfer-nft token-id)
```

* Called during `claim-inheritance` for each NFT

---

### ⚖️ Dispute Resolution

#### Raise Dispute

```clojure
(raise-dispute evidence-hash)
```

* Stores a hash (e.g., IPFS or SHA256) of evidence
* Marks the dispute as unresolved

#### Community Voting

```clojure
;; Uses `resolution-votes` and `dispute-metadata` to track
```

* Voters cast one vote per dispute
* If threshold (default `3`) is reached, `resolve-dispute` auto-triggers

---

### 🔄 Phased Inheritance

```clojure
(claim-phase-1 phase-data)
```

* Beneficiaries can receive inheritance in stages:

  * `phase-1-amount`, `phase-2-amount` (both percentages)
  * Enforced one-time claims per phase

---

## 🔐 Access Control

| Function                        | Restricted To       |
| ------------------------------- | ------------------- |
| `initialize-contract`           | Contract Owner      |
| `add-beneficiary`               | Contract Owner      |
| `update-will-hash`              | Contract Owner      |
| `deactivate-contract`           | Contract Owner      |
| `update-required-confirmations` | Contract Owner      |
| `confirm-death`                 | Listed Oracles Only |

---

## 📊 Data Structures

| Name                 | Description                                   |
| -------------------- | --------------------------------------------- |
| `beneficiaries`      | Per-user share, claim status, lock time, NFTs |
| `nft-ownership`      | Token ID → current owner                      |
| `oracles`            | Authorized death-verifiers                    |
| `disputes`           | Dispute metadata with resolution flag         |
| `inheritance-phases` | Phase tracking for staged inheritance         |
| `dispute-metadata`   | Voting count & timestamps for disputes        |
| `resolution-votes`   | Who has voted on which dispute                |

---

## ⚠️ Error Codes

| Error Code                       | Description                        |
| -------------------------------- | ---------------------------------- |
| `ERR-NOT-AUTHORIZED`             | Caller lacks required permissions  |
| `ERR-ALREADY-CLAIMED`            | Inheritance/NFT already claimed    |
| `ERR-DEATH-NOT-CONFIRMED`        | Not enough oracles confirmed death |
| `ERR-TIME-LOCK`                  | Claim attempted before unlock time |
| `ERR-INVALID-NFT`                | Invalid or missing NFT in claim    |
| `ERR-INSUFFICIENT-CONFIRMATIONS` | Not enough oracle approvals        |
| `ERR-INVALID-SHARE`              | Share exceeds 100%                 |

---

## 🧪 Example Flow

1. Owner deploys contract
2. Owner sets oracles via `initialize-contract`
3. Owner adds beneficiaries with shares, time-locks, NFTs
4. Oracles confirm death (≥ 2)
5. After lock, beneficiaries call `claim-inheritance`
6. If disputes arise, use `raise-dispute`, then community votes
7. Optionally, phased inheritance via `claim-phase-1`

---
