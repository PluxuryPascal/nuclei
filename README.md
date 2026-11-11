# Nuclei JS / SSH contribution evidence

Reproductions and local validation for two independent changes based on official `dev` revision `b2ea1489fd7decaa83e467febb1e20aa9de8b4d5`.

- Queue candidate: [source](https://github.com/PluxuryPascal/nuclei/tree/fix/js-admission-execution-budgets), commit `59752cf8a9e0372c99e25692fc139717cecad307`.
- SSH Info candidate: [source](https://github.com/PluxuryPascal/nuclei/tree/fix/ssh-info-context-cancellation), commit `29fefa9a9cad3df59fbb8c3bd1ebe6dfed75fd88`.

Each directory contains a tests-only reproduction patch, the complete independent fix patch, baseline/candidate outputs, and validation details. Local filesystem paths have been redacted. Tests use local JS execution or loopback TCP/SSH peers, not private scan data.

Apply either the tests-only patch to the base to reproduce the failure, or the full fix patch to test the candidate. Do not apply both on top of each other. The tests-only patches include controls that encode the proposed contract; read each issue/PR's compatibility discussion.

Changes are proposed upstream; this branch contains evidence only and is not intended to be merged into Nuclei.
