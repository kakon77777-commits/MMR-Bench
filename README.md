# MMR-Bench v1.0

**Multidirectional Matrix Representation benchmark for auditable spreadsheet reasoning, safe repair, XLSX snapshot validation, external-engine comparison, and signed calculation certificates.**

MMR-Bench began as a test of whether matrix position, direction, display structure, formula dependency, and semantic roles should be represented together rather than flattened into one linear sequence. v1.0 closes the first research cycle with a replayable certificate protocol.

## What v1.0 adds

- Safe bounded formula evaluator based on a Python AST whitelist.
- Excel-compatible `^` exponentiation semantics.
- Excel-style `ROUND` with half-away-from-zero behavior.
- Explicit 1900 / 1904 date-system handling in the MMR evaluator.
- Three-source evidence: OOXML cache, MMR evaluator, and an independent `formulas` engine.
- Ed25519-signed computation certificates.
- Canonical payload hashing (`MMR-CJ-1`).
- XLSX source-hash binding.
- Replay verification of normalization, formula evidence, and safety decisions.
- Tamper rejection and wrong-source rejection.
- Zero formula-write authorization from engine disagreement.

## Verified results

```text
69 tests passed
30/30 input mutation trials: MMR/external agreement
20/20 exponent trials: three-way agreement
20/20 ROUND trials: three-way agreement
20/20 1904 date trials: external-engine gap detected
4/4 signed example certificates: signature verified
4/4 signed example certificates: exact replay verified
10/10 tampered payloads rejected
10/10 wrong XLSX sources rejected
0 formula writes authorized by certificates
```

These are results on synthetic business contents stored in real XLSX packages. They do **not** establish arbitrary enterprise-workbook accuracy or complete Microsoft Excel compatibility.

## Quick start

```bash
python -m pip install -r requirements.txt
./run_v10.sh
```

Windows:

```bat
run_v10.bat
```

### Create a signing key

```bash
python -m mmr_bench.certificate_cli_v10 keygen \
  --private-key private.pem \
  --public-key public.pem
```

### Create and verify a certificate

```bash
python -m mmr_bench.certificate_cli_v10 create workbook.xlsx \
  --private-key private.pem \
  --output workbook.mmr-cert.json

python -m mmr_bench.certificate_cli_v10 verify workbook.mmr-cert.json
```

### Replay the certificate

```bash
python -m mmr_bench.certificate_cli_v10 replay \
  workbook.mmr-cert.json workbook.xlsx
```

## Certificate security boundary

A valid certificate proves that:

1. the certificate payload was signed by the included public key;
2. the payload hash is intact;
3. the replayed XLSX file has the recorded SHA-256 hash;
4. normalization, evidence, and decision digests reproduce under the recorded software environment.

It does **not** prove that any engine implements all Excel semantics, that a business formula is conceptually correct, or that an engine disagreement authorizes a formula rewrite.

## Main documents

- [`V10_RESULTS_ZH.md`](V10_RESULTS_ZH.md) — Chinese final report
- [`V10_RESULTS.md`](V10_RESULTS.md) — English final report
- [`CERTIFICATE_SPEC_V10.md`](CERTIFICATE_SPEC_V10.md) — certificate format
- [`REPLAY_PROTOCOL_V10.md`](REPLAY_PROTOCOL_V10.md) — verification and replay
- [`SECURITY_MODEL_V10.md`](SECURITY_MODEL_V10.md) — trust and threat model
- [`MANIFEST_V10.md`](MANIFEST_V10.md) — package contents
- [`GITHUB_PUBLISHING.md`](GITHUB_PUBLISHING.md) — repository publication steps

The v0.1–v0.9 code, reports, fixtures, and tests remain in the repository for reproducibility.

## Repository status

Research release. No general production write authorization. A software license has not yet been selected; public availability alone does not grant reuse rights.
