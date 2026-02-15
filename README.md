# Blockchain-Enabled Trust and Compliance for Clinical AI

**Decentralized Governance without Decentralized Data Storage**

[![Publication Pending](https://img.shields.io/badge/Publication-Pending-yellow)](.)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)

## Overview

This repository contains a reference implementation of a comprehensive governance framework for clinical artificial intelligence systems. The system provides **cryptographic auditability, consent enforcement, and reproducibility** without storing sensitive patient data on distributed ledgers.

### Key Innovation

Our framework separates **governance** (on-ledger, immutable, tamper-evident) from **data storage** (off-ledger, secure, privacy-preserving):

- ✅ **Hash commitments** on ledger (tamper detection)
- ✅ **Raw PHI/medical data** off-ledger (HIPAA/GDPR compliant)
- ✅ **Complete reproducibility** manifests (audit trail)
- ✅ **Machine-readable consent** policies (automated enforcement)
- ✅ **Cryptographic integrity** verification (hash-chained ledger)

## Features

### 🔐 Consent Management
- Machine-readable consent policies (`ConsentPolicy`)
- Role-based access control (RBAC)
- Purpose-based authorization
- Time-bounded validity
- Dynamic revocation support

### 📋 Immutable Audit Trail
- Append-only hash-chained ledger (`LocalLedger`)
- Cryptographic tamper detection
- Complete governance event history
- CONSENT_CREATED, CONSENT_REVOKED, DATA_ACCESSED, INFERENCE_EXECUTED events

### 🔬 Reproducibility Manifests
- Complete "receipts" for every AI inference
- Input/output artifact hashes
- Pipeline and model fingerprints
- Consent policy snapshots
- Training experiment logging

### 🛡️ Two-Tier Integrity Verification
- **On-ledger**: Hash chain verification
- **Off-ledger**: Manifest hash comparison
- Detects both ledger and storage tampering
- Validated via comprehensive ablation studies

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                  GovernanceOverlay                      │
│  (High-level API for consent, audit, reproducibility)  │
└───────────────────┬─────────────────────────────────────┘
                    │
        ┌───────────┴───────────┬─────────────────┐
        │                       │                 │
┌───────▼────────┐    ┌────────▼────────┐  ┌────▼─────────┐
│  LocalLedger   │    │  Policy Store   │  │Manifest Store│
│ (hash-chained) │    │  (off-ledger)   │  │ (off-ledger) │
│   ON-LEDGER    │    │   OFF-LEDGER    │  │  OFF-LEDGER  │
└────────────────┘    └─────────────────┘  └──────────────┘
     │                      │                      │
     │ Hashes only          │ Full policies        │ Full manifests
     │ Immutable            │ Encrypted storage    │ Large artifacts
     │ Tamper-evident       │ Privacy-preserving   │ Reproducibility
```

## Installation

### Requirements
- Python 3.11+
- Standard library only (no external dependencies)

### Quick Start

```bash
# Clone repository
git clone <repository-url>
cd blockChain

# Run demonstration
python -c "from system import demo; demo()"

# Or execute Jupyter notebook
jupyter notebook system.ipynb
```

## Usage

### Basic Example

```python
from system import LocalLedger, GovernanceOverlay, ConsentPolicy

# Initialize governance system
ledger = LocalLedger("governance_ledger.jsonl")
overlay = GovernanceOverlay(ledger)

# Create consent policy
policy = ConsentPolicy(
    policy_id="consent_001",
    subject_id="patient_pseudo_123",
    allowed_purposes=["clinical_care", "research"],
    allowed_roles=["dermatologist", "ml_engineer"],
    valid_from_ms=now_epoch_ms(),
    revocable=True
)
overlay.store_consent_policy(policy)

# Check authorization
authorized = overlay.check_consent(
    policy_id="consent_001",
    purpose="clinical_care",
    actor_role="dermatologist"
)

# Record AI inference with full reproducibility
repro = overlay.record_inference(
    actor_id="dr_jones",
    actor_role="dermatologist",
    purpose="clinical_care",
    consent_policy_id="consent_001",
    input_artifact_uri="s3://bucket/patient/image.jpg",
    input_artifact_hash="abc123...",
    output_artifacts={"report": ("s3://bucket/report.json", "def456...")},
    pipeline_manifest=pipeline,
    model_spec=model
)

# Verify ledger integrity
ok, error = ledger.verify_integrity()
```

### Running Scenario Simulations

```python
from system import run_scenario

# Simulate realistic clinical workload
results = run_scenario(
    overlay,
    n_patients=200,
    n_runs=2000,
    revoke_prob=0.02,
    tamper_after=False,
    seed=42
)
```

See `system.ipynb` for comprehensive examples and analysis.

## Production Deployment

The current `LocalLedger` is a **prototype** for demonstration. For production deployment, replace with:

### Recommended Platforms

| Platform | Type | Use Case | Privacy | Smart Contracts |
|----------|------|----------|---------|-----------------|
| **Hyperledger Fabric** | Permissioned | Healthcare consortia | Native | Chaincode (Go/Node.js) |
| **AWS QLDB** | Centralized | Single institution | AWS KMS | No (application layer) |
| **Ratio1** | Decentralized compute | Multi-institutional research | Privacy-preserving | Custom |
| **Ethereum + zkRollups** | Public + L2 | Maximum decentralization | Zero-knowledge proofs | Solidity |

### Migration Interface

The `LocalLedger` class provides a clean interface (`append()`, `verify_integrity()`) that can be swapped with any blockchain client implementing the same methods.

## Validation & Testing

### Comprehensive Test Suite

- ✅ **Ablation studies** - Component isolation testing
- ✅ **Multi-scenario stress testing** - Scale validation (50-1000 patients, 500-20k runs)
- ✅ **Consent stress testing** - High revocation rates (0-30%)
- ✅ **Integrity validation** - Tampering detection (100% detection rate)
- ✅ **Off-ledger verification** - Manifest corruption detection
- ✅ **Performance benchmarking** - Sub-millisecond governance overhead

### Sample Results

```
Scenario: 200 patients, 2000 runs, 2% revocation
- Executed: 1847 inferences
- Blocked: 153 unauthorized attempts
- Unauthorized block rate: 100%
- Avg governance overhead: 0.8ms per inference
- Ledger integrity: ✓ VERIFIED
- Tamper detection rate: 100%
```

## File Structure

```
blockChain/
├── system.ipynb              # Main implementation + demonstrations
├── elaboratedAnalysis.ipynb  # Statistical analysis & visualization
├── dataAnalysis.ipynb        # Additional metrics analysis
├── README.md                 # This file
├── LICENSE                   # Citation-required license
├── Analysis.MD               # Extended technical analysis
└── results_YYYYMMDD_HHMMSS/  # Timestamped experiment outputs
    ├── ledger/               # Hash-chained audit logs
    ├── manifest_store/       # Reproducibility manifests
    ├── policy_store/         # Consent policies
    ├── scenario_metrics.json # Multi-scenario statistics
    └── tamper_metrics.json   # Integrity validation results
```

## Citation

**Publication pending**. If you use this work, please cite:

```bibtex
@article{panagoulias2026blockchain,
  title={Blockchain-Enabled Trust and Compliance for Clinical AI: Decentralized Governance without Decentralized Data Storage},
  author={Panagoulias, Dimitrios P. and Damian, Andrei Ionut and Stamate, Cosmin and Toderian, Vitalii and Butusina, Petrica and De Franceschi, Alessandro and Bleotiu, Cristian and Sakkopoulos, Evangelos and Tsihrintzis, George A. and Virvou, Maria},
  journal={[Journal Name Pending]},
  year={2026},
  note={Publication pending}
}
```

## Authors

**Dimitrios P. Panagoulias¹**, Andrei Ionut Damian²,³, Cosmin Stamate²,⁴, Vitalii Toderian²,³, Petrica Butusina², Alessandro De Franceschi², Cristian Bleotiu², Evangelos Sakkopoulos¹, George A. Tsihrintzis¹, Maria Virvou¹

### Affiliations

1. **Department of Informatics, University of Piraeus**, 185 34, Greece  
   📧 panagoulias_d@unipi.gr, sakkopul@gmail.com, geoatsi@unipi.gr, mvirvou@unipi.gr

2. **Ratio1.ai**, Bucharest, Romania  
   📧 andrei.damian@ratio1.ai, vitalii.toderian@ratio1.ai, petrica.butusina@ratio1.ai, alessandro.defranceschi@ratio1.ai, cristian.bleotiu@ratio1.ai

3. **Computer Science and Information Technology Department, Faculty of Automatic Control and Computers, University POLITEHNICA of Bucharest**, Romania  
   📧 ionut_andrei.damian@upb.ro

4. **Birkbeck Knowledge Lab, Birkbeck College, University of London**, Malet Street, London, WC1E 7HX, UK  
   📧 c.stamate@bbk.ac.uk

## Acknowledgments

This work was partially supported by the **University of Piraeus Research Center** with EU Co-funding "Greece 2.0, SUB1.1: Clusters of Research Excellence (CREs), project No. YP3TA-0561098 entitled 'AI Data-Driven, Ethical, Environmental Performance - Enhanced Social Governance Analytics with Anonymity Protection'."

## License

This work is licensed under the [Creative Commons Attribution 4.0 International License](LICENSE) with the **additional requirement** that all academic or commercial use must include proper citation of the authors and original publication.

See [LICENSE](LICENSE) file for full terms.

## Contributing

This is research code associated with a pending publication. For collaboration inquiries, please contact the corresponding author at panagoulias_d@unipi.gr.

## Compliance & Standards

- **HIPAA** - No PHI on distributed ledger
- **GDPR** - Right to consent withdrawal, data minimization
- **FDA** - Audit trail for medical device software
- **ISO 13485** - Quality management traceability
- **DICOM** - Compatible with medical imaging standards

---

