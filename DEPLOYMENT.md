# Bradbury deployment evidence

Target: [GenLayer Bradbury](https://docs.genlayer.com/developers/networks), chain ID `4221`, RPC `https://rpc-bradbury.genlayer.com`.

- **Submission contract:** [`0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54`](https://explorer-bradbury.genlayer.com/address/0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54)
- Deployment transaction: [`0x831edb41467cfc0a000c4e7672c8f30fee1d5c3052d1056a6c6ad54a762cd4c9`](https://explorer-bradbury.genlayer.com/tx/0x831edb41467cfc0a000c4e7672c8f30fee1d5c3052d1056a6c6ad54a762cd4c9)
- Deployer: `0x5bb49021001200fe8156a81c7fcf097e535e7181`
- Local and deployed source SHA-256: `5f8e7c984f976204c2afffe8f59df132bcd2c8ab63275ac2cfbb4305537d4093`
- Deployed source length: 3,807 bytes. Bradbury returned the identical source and a schema with one nonpayable write method, `compare(string,string) -> string`, and no read methods.

## Verification performed on 14 October 2026

- `genlayer-test==0.29.2` direct-mode suite: **20 passed**, including Unicode line-separator and bidi-control rejection. The test fixture pins a published `v0.2.16` runner bundle that contains the contract's Bradbury dependency.
- `genvm-lint==0.11.0` with `GENVM_VERSION=v0.3.0-rc4`: lint and semantic validation passed; one write and zero view methods.
- Pyright 1.1.406 through `genvm-lint typecheck`: no type errors.
- Deployment receipt: `ACCEPTED`, `AGREE`, `FINISHED_WITH_RETURN`, five agree votes.

| Live case | Transaction | Observed result | Consensus/execution |
| --- | --- | --- | --- |
| Identical statements; no model needed | [`0x9f57866e…`](https://explorer-bradbury.genlayer.com/tx/0x9f57866e430a37fb3e9f92cbb2305983991114d3e55a3e3c0e9c72a67e195d87) | `EQUIVALENT` | `ACCEPTED`, successful return, five agree votes; trace shows zero LLM calls. |
| Quantity 20 → 30 | [`0x31c9b157…`](https://explorer-bradbury.genlayer.com/tx/0x31c9b157c70303be8d85e1fd7a593ed059895c349c49b305e1df15407a9b17a5) | `MATERIAL_CHANGE` | `ACCEPTED`, successful return, three agree votes and two validator timeouts. |
| Wording paraphrase | [`0xb7adea14…`](https://explorer-bradbury.genlayer.com/tx/0xb7adea14d3a686f08bb61f0ed194dd1fb541c8205cbc7fadc706f6828935a77a) | `EQUIVALENT` | `ACCEPTED`, successful return, three agree votes and two validator timeouts. |
| Missing context (“Monday” → “then”) | [`0x8e7a88dd…`](https://explorer-bradbury.genlayer.com/tx/0x8e7a88dda71e26ff67de350ad29bd99e338f07b97edd983c19c5ef74c43c3763) | `UNCERTAIN` | `ACCEPTED`, successful return, four agree votes and one validator timeout. |
| Embedded Unicode line separator | [`0x0507fe27…`](https://explorer-bradbury.genlayer.com/tx/0x0507fe27cd8347b9459157bd628f8dced598e0e30d15452b4ed93916230caf5d) | Rejected with `REVISION_CONTROL` | `ACCEPTED` consensus on a `FINISHED_WITH_ERROR` receipt, five disagree votes. This is the expected guard behavior, **not** a successful comparison. |

The successful verdicts above were read from Bradbury's execution traces, not inferred from the CLI's “write operation successfully executed” line. The CLI can print that line for a consensus-accepted user error, as the negative test demonstrates. Some validators timed out on real AI calls; sufficient remaining validators agreed, but this confirms the availability limitation in [`SECURITY.md`](SECURITY.md).

An earlier audit build at [`0xd57aeEe92923e591Dfb026211c5d600528F8aB6D`](https://explorer-bradbury.genlayer.com/address/0xd57aeEe92923e591Dfb026211c5d600528F8aB6D) was **superseded** after review found that its ASCII-only control check admitted Unicode line separators. Do not submit or integrate that address. The corrected source is the submission contract above.

## Finality and reproducibility

At the last check, these transactions were `ACCEPTED` (status 5), **not `FINALIZED`** (status 7). [GenLayer's finality documentation](https://docs.genlayer.com/understand-genlayer-protocol/core-concepts/optimistic-democracy/finality) says accepted results remain provisional during the appeal window. Recheck the explorer or `genlayer receipt <hash>` before relying on them as final. The deployment and tests are real, but this document does not claim finality prematurely.

The CLI v0.39.2 `estimate-fees` command failed against its bundled SDK with `client.estimateTransactionFees is not a function`; actual deployment and writes succeeded without a custom fee preset. This is a tooling caveat, not a finding about MeaningSeal. The CLI's selected network is global state and can be changed by another session, so verify `genlayer network info` before each write, even when passing `--rpc`.
