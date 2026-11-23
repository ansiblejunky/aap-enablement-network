# AAP Enablement: Network Automation

Hands-on network automation enablement for Red Hat consultants using Red Hat Ansible Automation Platform (AAP).

Build the skills to discover network devices, collect operational data, manage configurations, and turn playbooks into repeatable automation services. Start with lightweight simulated devices on a laptop, then progress to virtual or physical lab devices and AAP.

## Project status

This repository is being established as a dedicated home for network automation enablement. The learning path and directory layout below describe the intended content; labs will become available as they are added and validated.

The FakeNOS quick start below can be used independently while the lab content is being built.

## Who this is for

- Consultants learning or refreshing Ansible network automation skills.
- Workshop facilitators building repeatable hands-on environments.
- Practitioners preparing demonstrations and delivery patterns for customer engagements.

You should be comfortable with basic Git, YAML, Ansible inventories and playbooks, and network concepts such as interfaces, VLANs, routing, and SSH. Prior AAP experience is helpful for the later labs.

## Learning outcomes

By working through the planned labs, you will learn to:

- Choose network collections, connection plugins, and inventory variables for a target platform.
- Collect device information and turn command output into useful data.
- Back up device configurations and evaluate configuration differences.
- Use network resource modules to express intended configuration where supported.
- Validate changes and explain idempotency, check mode, and rollback limitations.
- Package dependencies in an automation execution environment.
- Run automation through AAP projects, inventories, credentials, job templates, and workflows.
- Troubleshoot connectivity, privilege escalation, dependency, and module failures.

## Choose your lab environment

| Environment | Best suited to | Considerations |
| --- | --- | --- |
| FakeNOS | Getting started, SSH and CLI interactions, command output parsing, repeatable demonstrations | Returns simulated responses; commands and behavior must match the exercise. |
| Containerlab with suitable network OS images | Configuration management, state validation, and network behavior | Requires a suitable container host and device images; requirements depend on the chosen platform. |
| Existing virtual or physical lab | Platform-specific validation and delivery practice | Requires authorized access, credentials, and a recovery plan for configuration exercises. |

[FakeNOS](https://github.com/fakenos/fakenos) is the starting point for lightweight local exercises. It simulates network CLI interactions over SSH without requiring a full network operating system image. It replaces the role previously filled by `fake-switches` in the original onboarding material.

FakeNOS responds to commands with predefined output. A successful configuration command does not establish that a device changed state, that a second run will be idempotent, or that traffic will flow. Use virtual or physical devices for exercises that require those behaviors. See the [FakeNOS simulation model](https://github.com/fakenos/fakenos#what-not).

[Containerlab](https://containerlab.dev/) provides an option for building topologies with network operating system images. Select images appropriate to the exercise and follow their access and licensing requirements.

## Quick start: explore a simulated device

This standalone exercise starts FakeNOS and connects to its default simulated Cisco IOS device. It does not require AAP or any additional files from this repository.

### 1. Create a local Python environment

Use Python 3.11–3.14 for the pinned FakeNOS version below. See its [published package requirements](https://pypi.org/project/fakenos/1.2.0/).

From your local checkout, run these commands in a POSIX shell:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install fakenos==1.2.0
```

On Windows, use a compatible shell or adapt the virtual environment activation command for PowerShell.

### 2. Start the simulated devices

Run the following in the activated environment and leave the terminal open:

```bash
python - <<'PY'
from fakenos import FakeNOS

with FakeNOS():
    input("FakeNOS is running. Press Enter to stop.\n")
PY
```

This uses the [upstream default inventory](https://github.com/fakenos/fakenos/blob/master/fakenos/core/fakenos.py), which includes a Cisco IOS simulation on port `6000`. The example follows the upstream Python API; validate it in your chosen workstation environment before facilitating a workshop.

### 3. Connect from a second terminal

```bash
ssh -p 6000 user@127.0.0.1
```

The default password is `user`. These credentials are for this local simulation only.

Try `show version`, then exit the SSH session. Return to the first terminal and press Enter to stop the simulator.

If port `6000` is already occupied, stop the conflicting lab process or define a custom FakeNOS inventory with different ports.

### 4. Progress to Ansible

The first Ansible lab should introduce an inventory, the required collections, an SSH backend, and a playbook that runs a supported show command. It should document the simulator commands it depends on and the expected output.

When running Ansible inside an execution environment or from AAP, `127.0.0.1` refers to that execution context. Provide an address and port reachable from the execution node, container, or pod that runs the job. AAP jobs cannot reach a consultant's laptop simulator through the controller's loopback address.

## Planned learning path

Work through the labs in sequence, or use an individual lab after meeting its prerequisites. All entries below are planned until their instructions and supporting files are added.

| Lab | Focus | Expected evidence | Target environment |
| --- | --- | --- | --- |
| 01 — Connect and collect | Inventory, network connections, credentials, show commands | A successful connection and captured command output | FakeNOS |
| 02 — Parse and report | Structured facts, parsing, assertions, templates | A report generated from known device responses | FakeNOS |
| 03 — Back up configurations | Configuration collection and artifact handling | A configuration snapshot for each target | FakeNOS with suitable command fixtures, or a lab device |
| 04 — Manage intended state | Resource modules, configuration differences, idempotency | A verified change and a second run demonstrating the intended behavior | Virtual or physical lab devices |
| 05 — Validate and recover | Prechecks, postchecks, failure handling, recovery | Documented validation results and a tested recovery procedure | Virtual or physical lab devices |
| 06 — Build an execution environment | Collection and Python dependencies, image builds | An image that successfully runs the selected labs | Container build environment and reachable lab devices |
| 07 — Operationalize with AAP | Projects, inventories, credentials, templates, workflows | An AAP job with reviewable results | AAP and reachable lab devices |

Each lab should include its objective, estimated duration, prerequisites, topology, steps, expected results, troubleshooting, cleanup, and facilitator notes. Configuration labs should also describe recovery and how to verify the resulting device state.

## Proposed repository layout

Create these directories as their content is introduced:

```text
aap-enablement-network/
├── README.md
├── collections/
│   └── requirements.yml       # Ansible collection dependencies
├── requirements.txt          # Python dependencies for local tooling
├── execution-environment/
│   └── execution-environment.yml
├── inventories/
│   └── lab/                  # Example inventory and nonsecret variables
├── labs/                     # Numbered exercise instructions
├── playbooks/                # Playbooks used by the exercises
├── roles/                    # Shared automation introduced by the labs
├── simulator/                # FakeNOS inventories and command fixtures
└── docs/                     # Architecture, troubleshooting, facilitator notes
```

Keep simulator definitions separate from Ansible inventory: one defines the fake devices, while the other tells Ansible how to connect to them.

## Working practices

- Keep credentials in AAP credential objects, Ansible Vault, or an approved secret store. Commit only explicitly identified lab credentials.
- Target a dedicated lab inventory and use host limits when practicing configuration changes.
- Keep generated backups, reports, local virtual environments, and sensitive device output out of version control by default.
- Review module documentation for check mode, diff mode, and supported states before relying on them in an exercise.
- Document the exact collections, execution environment, device platforms, and versions used to validate each lab.
- Identify which dependencies and device platforms are community projects and which are covered by an applicable Red Hat support offering.

## Contributing

Contributions should help another consultant reproduce an exercise and explain why the automation behaves as it does.

For a new or updated lab:

1. State the learning objective and required device behavior.
2. Include the smallest useful inventory, playbook, and simulator fixtures or topology.
3. Document dependencies, expected output, cleanup, and common failures.
4. Validate from a clean environment and record the versions used.
5. For configuration exercises, verify the resulting state and repeat-run behavior on a suitable device.

Use sanitized examples. Share customer-derived content only when authorized and after removing identifying information and secrets.

## References

- [Red Hat Ansible Automation Platform documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/)
- [Ansible network automation documentation](https://docs.ansible.com/ansible/latest/network/index.html)
- [Ansible network resource modules](https://docs.ansible.com/ansible/latest/network/user_guide/network_resource_modules.html)
- [Ansible Builder documentation](https://ansible.readthedocs.io/projects/builder/en/stable/)
- [FakeNOS source and examples](https://github.com/fakenos/fakenos)
- [Containerlab documentation](https://containerlab.dev/)

## Project ownership and license

Maintainers should add the owning team, contribution contact, and an approved `LICENSE` file before broader distribution. References to Red Hat products describe the enablement focus of this repository; this README does not establish product support coverage for its examples or third-party tools.

