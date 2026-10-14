# GenLayer Portal submission — Intelligent Contract

Use the **Intelligent Contract** category. This file is a reviewer-friendly summary, not an assertion that the Portal has pre-approved the work. Check any additional rules shown in the signed-in submission form before submitting.

## Paste-ready description

**MeaningSeal** is a standalone, deployed GenLayer intelligent contract for public semantic-change attestations. It compares two short statements and returns `EQUIVALENT`, `MATERIAL_CHANGE`, or `UNCERTAIN`. Exact matches take a deterministic path; other cases use independently executed validator classifications and a fixed three-value consensus output. Its intended use is a shared, finalizable record when parties need to know whether a reworded policy, bounty term, or agreement clause changed materially. It does not claim to prove truth or legal equivalence, and it does not hold funds or perform settlement.

## Evidence to paste into Portal fields

| Field | Value |
| --- | --- |
| Public repository | https://github.com/Manablaq/meaning-seal |
| Contract source on `main` | https://github.com/Manablaq/meaning-seal/blob/main/contracts/meaning_seal.py |
| Network | Bradbury testnet, chain ID `4221` |
| Contract address | `0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54` |
| Explorer contract | https://explorer-bradbury.genlayer.com/address/0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54 |
| Deployment transaction | https://explorer-bradbury.genlayer.com/tx/0x831edb41467cfc0a000c4e7672c8f30fee1d5c3052d1056a6c6ad54a762cd4c9 |
| Live `MATERIAL_CHANGE` transaction | https://explorer-bradbury.genlayer.com/tx/0x31c9b157c70303be8d85e1fd7a593ed059895c349c49b305e1df15407a9b17a5 |
| Live `EQUIVALENT` transaction | https://explorer-bradbury.genlayer.com/tx/0xb7adea14d3a686f08bb61f0ed194dd1fb541c8205cbc7fadc706f6828935a77a |
| Live `UNCERTAIN` transaction | https://explorer-bradbury.genlayer.com/tx/0x8e7a88dda71e26ff67de350ad29bd99e338f07b97edd983c19c5ef74c43c3763 |
| Full test/receipt evidence | https://github.com/Manablaq/meaning-seal/blob/main/DEPLOYMENT.md |
| Reproducible direct-mode tests | https://github.com/Manablaq/meaning-seal/blob/main/tests/test_meaning_seal_direct.py |
| Passing CI | https://github.com/Manablaq/meaning-seal/actions/runs/38568340625 |
| Security limits | https://github.com/Manablaq/meaning-seal/blob/main/SECURITY.md |

The source SHA-256 is `5f8e7c984f976204c2afffe8f59df132bcd2c8ab63275ac2cfbb4305537d4093`. The source and deployed code were compared. Deployment and 14 live test transactions are finalized; expected invalid-input transactions have error execution results, not successful verdicts. The direct-mode suite passes 27 tests.

## One-minute reviewer path

Open the source and contract explorer links above; inspect the finalized deployment and one successful comparison transaction. Confirm `FINALIZED` plus `FINISHED_WITH_RETURN` for a verdict. The transaction trace contains the result. To reproduce locally, follow the Python 3.12 commands in [`README.md`](README.md#reproduce-tests). To submit a new live call, use the Bradbury instructions in [`README.md`](README.md#bradbury-deployment).

## Claims to avoid

- Do not submit the superseded `0xd57aeEe92923e591Dfb026211c5d600528F8aB6D` address.
- Do not claim zero weaknesses, guaranteed semantic accuracy, or immunity to prompt injection.
- Do not claim MeaningSeal stores verdicts in contract state, settles funds, or has a working frontend. Its verdict is in the finalized transaction receipt.
- Do not present expected invalid-input errors as successful comparisons merely because consensus finalized them.

Before submitting, confirm that the Portal profile is linked to the intended wallet and that every required field in the current **Intelligent Contract** form is satisfied. Public GenLayer pages do not expose all signed-in form requirements.
