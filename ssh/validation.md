# SSH validation

Base: official dev b2ea1489fd7decaa83e467febb1e20aa9de8b4d5; Linux amd64; Go 1.27.3.

| Check | Result |
| --- | --- |
| Exact final added tests against pristine production | Expected FAIL: cancellation, JS native socket lifetime, deadline/cache/caller isolation assertions |
| Entire SSH package, count=10 | PASS, 5.506s (final tests repeated ten times) |
| Entire SSH package, race count=5 | PASS, 8.730s |
| Entire compiler and SSH packages | PASS, 13.083s and1.462s |
| go vet ./pkg/js/libs/ssh | PASS |
| Scoped memogen run | PASS; no remaining @memo annotations; stale wrapper explicitly deleted |
| Independent application of fix.patch to pristine base | PASS |
| Entire SSH package in independently patched checkout | PASS |
| go build -o <local-output> ./cmd/nuclei | PASS (final-source build repeated) |
| git diff --check | PASS |

Commands:

```sh
GOWORK=off go test ./pkg/js/libs/ssh -run 'TestSSHInfo|TestJSDeadlineClosesSSHConnection' -count=1 -v -timeout=60s
GOWORK=off go test ./pkg/js/libs/ssh -count=10 -timeout=90s
GOWORK=off go test -race ./pkg/js/libs/ssh -count=5 -timeout=120s
GOWORK=off go test ./pkg/js/compiler ./pkg/js/libs/ssh -count=1 -timeout=180s
GOWORK=off go vet ./pkg/js/libs/ssh
GOWORK=off go run ./cmd/memogen -src pkg/js/libs/ssh -tpl cmd/memogen/function.tpl
```

The baseline uses exactly the final candidate test file without the production fix. Some checks (independent concurrent connections, canceled cache access) also encode the proposed ownership contract and must be reviewed as behavior choices, not independent proof that the old cache policy was itself a bug. The central original defect is separately demonstrated by the native cancellation and actual JS socket-lifetime tests.

At initial review the full Makefile targets had not been run. See ../preflight.md for publication checks: make vet and make build now PASS; full make test is not green. The CLI build above and targeted vet are narrower checks; they do not represent complete Makefile targets or upstream CI. No full scan, authenticated SSH fix or concurrency throughput benefit is claimed. Network operations remain per-caller for uncached requests; this can increase connections.
