# Queue validation

Base: official dev b2ea1489fd7decaa83e467febb1e20aa9de8b4d5; Linux amd64; Go 1.27.3.

| Check | Result |
| --- | --- |
| Exact added regression file against pristine production | Expected FAIL, only pooled/non-pooled quick-script-after-queue fail |
| Added regressions and controls, count=10 | PASS, 16.729s |
| Added regressions and controls, race count=3 | PASS, 9.266s |
| Entire compiler package | PASS, 13.250s; no skips reported |
| Occupied-pool resizing control, race count=10 | PASS |
| go vet ./pkg/js/compiler | PASS |
| Independent application of fix.patch to pristine base | PASS |
| Added tests in independently patched checkout | PASS |
| go build -o <local-output> ./cmd/nuclei | PASS (final-source build repeated) |
| git diff --check | PASS |

All Go commands used GOWORK=off. The attached logs record before/after/race/package checks. Exact focused commands are in pr.md. The baseline and candidate use the same final test file.

The current-patch regression is queue200ms + script150ms under a300ms per-phase budget. The earlier exploratory750ms queue /250ms budget example is not claimed fixed: this bounded-admission proposal intentionally rejects admission after250ms in that case.

At initial review the full Makefile targets had not been run. See ../preflight.md for publication checks: make vet and make build now PASS; full make test is not green. The CLI build above and targeted vet are narrower checks; they are not presented as those complete Makefile targets or as completed upstream CI. Network-based integration and full scanning behavior were not measured. The repeated compiler test uses a deliberately small pool, not changed production defaults.
