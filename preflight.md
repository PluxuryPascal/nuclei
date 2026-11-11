# Publication preflight (2026-11-11)

- `GOWORK=off GOMAXPROCS=2 make vet GOFLAGS=-p=2`: PASS for both candidate branches (module download/verification and repository-wide go vet).
- `GOWORK=off GOMAXPROCS=2 make build`: PASS for both candidate branches. Temporary build files were placed on the main disk. These are the actual upstream Makefile targets, in addition to the earlier CLI go build checks.
- Focused/native/real-JS regression checks, package suites, repeated/race checks and independent patch application: PASS as listed in each topic's validation.
- Full `make test` is **not reported as passing**. The first concurrent runs exhausted the local tmpfs during linking. A sequential disk-backed queue run used `-race -p=2 -v -timeout=3m -count=1` and encountered:
  - `lib`: package deadline while downloading Chromium for TestHeadlessOptionInitialization.
  - `lib/tests`: package deadline during remote scanme.sh/honey.scanme.sh SDK tests.
  - `pkg/external/customtemplates`: TestDownloadCustomTemplatesFromGitHub cannot obtain the expected nuclei-templates-test fixture. The same test also FAILS on the unchanged base revision.
  - `pkg/fuzz`: tests requiring external Interactsh registration fail when the service request times out.
  - `pkg/installer`: TestVersionCheck times out querying api.pdtm.sh.
- The broad rerun was stopped after these external-service failures rather than continuing unrelated network tests. The changed compiler package did pass within that broad race-enabled run. No upstream tests were edited or selectively changed to make this run green. The analogous SSH broad rerun was not duplicated after these shared environment failures.

This is a local environment limitation report, not a claim that every full-suite failure has been independently reproduced on baseline. Only the custom-template fixture failure received a separate baseline run. Full upstream CI results remain pending.
