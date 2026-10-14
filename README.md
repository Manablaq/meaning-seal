# MeaningSeal

MeaningSeal is a standalone GenLayer intelligent contract for bounded, one-shot semantic-change attestations. Send two short statements to `compare(original, revision)` and receive one of `EQUIVALENT`, `MATERIAL_CHANGE`, or `UNCERTAIN`. Exact byte-for-byte matches use a deterministic path; other comparisons ask validators to classify the same public input independently. The contract holds no funds, stores no user state, and exposes no read/getter method.

This design avoids the Studio Next read-call issue encountered in the earlier project: there is no contract getter to call, and the deployment target is **Bradbury (chain ID 4221)**, not Studio Next. It does **not** guarantee immunity from Bradbury RPC outages, model failures, validator disagreement, or future protocol changes.

Submission deployment: [`0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54`](https://explorer-bradbury.genlayer.com/address/0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54). The full transaction and test evidence is in [`DEPLOYMENT.md`](DEPLOYMENT.md). An earlier address was superseded and must not be used.

## Contract interface

| Method | Kind | Inputs | Result |
| --- | --- | --- | --- |
| `compare` | write transaction | `original: str`, `revision: str` | one of the three verdict strings |

The constructor has no arguments. Inputs must each be 8–512 Unicode code points, a single line, with no leading/trailing whitespace or non-printing control/format characters. Both inputs are public transaction data; do not submit secrets or personal information. A verdict is an AI-assisted comparison, **not** proof that a statement is true, legally equivalent, or safe to rely on.

The source is [`contracts/meaning_seal.py`](contracts/meaning_seal.py). The `Depends` header pins the Bradbury-compatible `py-genlayer` runner. There is no upgrade path or admin role.

## Reproduce tests

Use Python 3.12 and the stable GenLayer test package:

```sh
python3.12 -m venv .venv
.venv/bin/python -m pip install -r requirements-dev.txt
GENVM_VERSION=v0.3.0-rc4 .venv/bin/pytest tests/ -q
```

`genvm-lint` can additionally check the contract with `GENVM_VERSION=v0.3.0-rc4 genvm-lint check contracts/meaning_seal.py`. Local mocked-model tests establish input handling and validator logic; they do not establish real-world model accuracy or network availability.

The GitHub Actions workflow runs the direct-mode test suite on pushes and pull requests. The deployment evidence in [`DEPLOYMENT.md`](DEPLOYMENT.md) also records a separate semantic-validation and type-check pass.

## Bradbury deployment

The official GenLayer CLI v0.39.2 can deploy with:

```sh
genlayer network set testnet-bradbury
genlayer account show
genlayer deploy --contract contracts/meaning_seal.py --rpc https://rpc-bradbury.genlayer.com
```

Use an unlocked, funded account and record the resulting transaction hash and address. Do not put keystores, private keys, or `.env` files in this repository. See [`DEPLOYMENT.md`](DEPLOYMENT.md) for the verified deployment and readback evidence.

For calls containing only digits, use a typed SDK client that sends both arguments as strings. The CLI infers a bare numeric argument as an integer even when shell-quoted, so such a call is rejected with `ORIGINAL_TYPE` or `REVISION_TYPE`. Always inspect both the consensus status and execution result: a finalized or accepted transaction can still contain an expected user error.

## How to interpret the result

`EQUIVALENT` means the validators classified the claims as materially the same; `MATERIAL_CHANGE` means they classified a material difference; `UNCERTAIN` means ambiguity or missing context prevented a reliable decision. A write transaction can also fail or remain unfinalized. Only treat a finalized successful receipt as a recorded result. The receipt and transaction calldata are the audit trail; the contract intentionally provides no on-chain history lookup.

## Security and limitations

See [`SECURITY.md`](SECURITY.md) for the threat model, known limitations, and reporting guidance. In particular, prompt injection and erroneous model consensus cannot be eliminated by this contract. For high-stakes decisions, obtain independent human review.
