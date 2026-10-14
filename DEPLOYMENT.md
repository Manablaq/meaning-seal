# Bradbury deployment evidence

Target: [GenLayer Bradbury](https://docs.genlayer.com/developers/networks), chain ID `4221`, RPC `https://rpc-bradbury.genlayer.com`.

- **Submission contract:** [`0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54`](https://explorer-bradbury.genlayer.com/address/0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54)
- Deployment transaction: [`0x831edb41467cfc0a000c4e7672c8f30fee1d5c3052d1056a6c6ad54a762cd4c9`](https://explorer-bradbury.genlayer.com/tx/0x831edb41467cfc0a000c4e7672c8f30fee1d5c3052d1056a6c6ad54a762cd4c9)
- Deployer: `0x5bb49021001200fe8156a81c7fcf097e535e7181`
- Local and deployed source SHA-256: `5f8e7c984f976204c2afffe8f59df132bcd2c8ab63275ac2cfbb4305537d4093`
- Deployed source length: 3,807 bytes. Bradbury returned the identical source and a schema with one nonpayable write method, `compare(string,string) -> string`, and no read methods.

## Verification performed on 14 October 2026

- `genlayer-test==0.29.2` direct-mode suite: **27 passed**, including exact 8/512-character boundaries, printable Unicode, numeric argument types, malformed model output, Unicode line-separator and bidi-control rejection. The test fixture pins a published `v0.2.16` runner bundle that contains the contract's Bradbury dependency.
- `genvm-lint==0.11.0` with `GENVM_VERSION=v0.3.0-rc4`: lint and semantic validation passed; one write and zero view methods.
- Pyright 1.1.406 through `genvm-lint typecheck`: no type errors.
- Deployment receipt: `FINALIZED`, `AGREE`, `FINISHED_WITH_RETURN`, five agree votes.

| Live case | Transaction | Observed result | Consensus/execution |
| --- | --- | --- | --- |
| Identical statements; no model needed | [`0x9f57866e…`](https://explorer-bradbury.genlayer.com/tx/0x9f57866e430a37fb3e9f92cbb2305983991114d3e55a3e3c0e9c72a67e195d87) | `EQUIVALENT` | `FINALIZED`, successful return, five agree votes; trace shows zero LLM calls. |
| Quantity 20 → 30 | [`0x31c9b157…`](https://explorer-bradbury.genlayer.com/tx/0x31c9b157c70303be8d85e1fd7a593ed059895c349c49b305e1df15407a9b17a5) | `MATERIAL_CHANGE` | `FINALIZED`, successful return, three agree votes and two validator timeouts. |
| Wording paraphrase | [`0xb7adea14…`](https://explorer-bradbury.genlayer.com/tx/0xb7adea14d3a686f08bb61f0ed194dd1fb541c8205cbc7fadc706f6828935a77a) | `EQUIVALENT` | `FINALIZED`, successful return, three agree votes and two validator timeouts. |
| Missing context (“Monday” → “then”) | [`0x8e7a88dd…`](https://explorer-bradbury.genlayer.com/tx/0x8e7a88dda71e26ff67de350ad29bd99e338f07b97edd983c19c5ef74c43c3763) | `UNCERTAIN` | `FINALIZED`, successful return, four agree votes and one validator timeout. |
| Embedded Unicode line separator | [`0x0507fe27…`](https://explorer-bradbury.genlayer.com/tx/0x0507fe27cd8347b9459157bd628f8dced598e0e30d15452b4ed93916230caf5d) | Rejected with `REVISION_CONTROL` | `FINALIZED` consensus on a `FINISHED_WITH_ERROR` receipt, five disagree votes. This is the expected guard behavior, **not** a successful comparison. |

The successful verdicts above were read from Bradbury's execution traces, not inferred from the CLI's “write operation successfully executed” line. The CLI can print that line for a consensus-accepted user error, as the negative test demonstrates. Some validators timed out on real AI calls; sufficient remaining validators agreed, but this confirms the availability limitation in [`SECURITY.md`](SECURITY.md).

An earlier audit build at [`0xd57aeEe92923e591Dfb026211c5d600528F8aB6D`](https://explorer-bradbury.genlayer.com/address/0xd57aeEe92923e591Dfb026211c5d600528F8aB6D) was **superseded** after review found that its ASCII-only control check admitted Unicode line separators. Do not submit or integrate that address. The corrected source is the submission contract above.

## Additional Bradbury boundary and adversarial tests

The following are **real write transactions** against the same submission contract, not local simulations. A follow-up Bradbury RPC check confirmed that all nine are `FINALIZED`. Their observed verdict/error came from execution receipts or traces; finality alone does not mean execution succeeded. The final receipts retained the expected successful-return or error execution outcomes.

| Case | Transaction | Observed execution |
| --- | --- | --- |
| 8 printable characters, exact match | [`0x68741075…`](https://explorer-bradbury.genlayer.com/tx/0x68741075be9f89fbb7512c782290125d5fb7064ba2fbb8ff2239323a7c051fe6) | Returned `EQUIVALENT`. |
| 512 printable characters, exact match | [`0x477e16b8…`](https://explorer-bradbury.genlayer.com/tx/0x477e16b8dc7154c3ca8c2479a86be4b892ef02d28ec0e51194c13493412c6b9c) | Returned `EQUIVALENT`. |
| 7 characters | [`0x1f81cb32…`](https://explorer-bradbury.genlayer.com/tx/0x1f81cb32b04aeb4b54072d56acaf502f2633c138f4dcf282c6ea9d0809a694ee) | Rejected with `ORIGINAL_SIZE`. |
| 513 characters | [`0x2a2b6f39…`](https://explorer-bradbury.genlayer.com/tx/0x2a2b6f39907025b24b5e5db4ae56900e845bf90708c27f1e5c1fbb6049b5a971) | Rejected with `ORIGINAL_SIZE`. |
| Leading space | [`0xb0891503…`](https://explorer-bradbury.genlayer.com/tx/0xb089150314e87bff09296b266720fa35c00287bb3356ba1d83fcd6214d4c0e8c) | Rejected with `ORIGINAL_WHITESPACE`. |
| Printable Unicode, exact match | [`0x5f574a4b…`](https://explorer-bradbury.genlayer.com/tx/0x5f574a4ba2bdddf1dd8321a0db712a0ed47e80a32f4d7ef5ecde5f90745a29a4) | Returned `EQUIVALENT`. |
| Negation of approval | [`0x655746c1…`](https://explorer-bradbury.genlayer.com/tx/0x655746c1db61d4648952306e0f92744a2c4c74eb372ed3625f52f8377ad43002) | Returned `MATERIAL_CHANGE`. |
| Embedded instruction to return `EQUIVALENT` despite changing a fee | [`0xb26df41e…`](https://explorer-bradbury.genlayer.com/tx/0xb26df41ed0276cb9f72f4667426b6d9435c0d3cc5f650ee92001b32a35fabbca) | Returned `MATERIAL_CHANGE`; this one input did not redirect the model. |
| Bare numeric CLI arguments | [`0x3b1bb618…`](https://explorer-bradbury.genlayer.com/tx/0x3b1bb618edb4dac475d37e089a92c909efb06cae89a065d2cd49f49be51b6885) | Rejected with `ORIGINAL_TYPE`: the CLI inferred integers for `12345678`, even though the caller intended strings. |

The negative rows above are expected `FINISHED_WITH_ERROR` receipts, **not** successful comparisons. The CLI's numeric coercion is a client-side usability caveat: use a typed SDK call with string arguments for digit-only statements. Shell quoting alone does not force the CLI to send a string.

## Finality and reproducibility

An independent Bradbury RPC read confirmed that the deployment and all fourteen test transactions are `FINALIZED` (status 7). Five initial tests and nine additional tests are listed above. Finality is distinct from execution success: the invalid-input and wrong-type tests remain `FINISHED_WITH_ERROR` by design. See [GenLayer's finality documentation](https://docs.genlayer.com/understand-genlayer-protocol/core-concepts/optimistic-democracy/finality) for the protocol's finality process.

The CLI v0.39.2 `estimate-fees` command failed against its bundled SDK with `client.estimateTransactionFees is not a function`; actual deployment and writes succeeded without a custom fee preset. This is a tooling caveat, not a finding about MeaningSeal. The CLI's selected network is global state and can be changed by another session, so verify `genlayer network info` before each write, even when passing `--rpc`.

The Bradbury RPC returned `method not found` for `gen_getTransactionLifecycle` on this check, despite that method appearing in current protocol documentation. The standard `gen_getTransactionStatus`, receipt, and trace methods worked. Do not infer finalization readiness from elapsed time alone.
