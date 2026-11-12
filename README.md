# Automated server decommissioning — Ansible skeleton

Companion to `../ansible-provisioning` for the ServiceNow → AAP proof of concept.
**This project does not remove servers or change external systems.** Jobs validate
basic request fields and print integration checklists. Successful previews do not
prove approval, readiness, deletion, cleanup or retirement.

## Playbooks

All job playbooks reside in `playbooks/`.

| Job | File | Purpose |
|---|---|---|
| 1 | `01_validate_targets.yml` | Validate decommissioning targets |
| 2 | `02_confirm_retirement_readiness.yml` | Confirm retirement readiness |
| 3 | `03_withdraw_application_services.yml` | Withdraw application services |
| 4 | `04_cleanup_host.yml` | Complete host-side cleanup |
| 5 | `05_remove_compute.yml` | Remove compute and verify storage disposition |
| 6 | `06_retire_enterprise_records.yml` | Retire identity certificate and management records |
| 7 | `07_release_network_inventory.yml` | Release network allocations and retire inventory |
| 8 | `08_verify_completion.yml` | Verify decommissioning completion and handoff |

`90_handle_failure.yml` provides a failure-routing scaffold. `site.yml` runs the
sequential local walkthrough. AAP uses individual job templates and success/failure edges.

## Local walkthrough

From this project folder, with ansible-core installed:

```sh
ansible-playbook playbooks/site.yml -e @vars/request.example.yml --syntax-check
ansible-playbook playbooks/site.yml -e @vars/request.example.yml
ansible-playbook playbooks/01_validate_targets.yml -e @vars/request.example.yml
```

Copy `vars/request.example.yml` to ignored `vars/request.local.yml` for local values
and pass `-e @vars/request.local.yml`. Example identifiers, evidence references and
dates are synthetic. Basic field checks do not validate evidence or enforce time windows.
The preview lists planned work only, including conditional branches.

## Structure

- `inventories/poc/`: localhost orchestration inventory.
- `vars/`: safe preview defaults and a two-server sample request.
- `playbooks/tasks/`: shared request checks and preview artifact output.
- `roles/`: documented integration role boundaries to implement.
- `templates/retirement_record.json.j2`: planned retirement record template, not completion evidence.
- `collections/requirements.yml`: add approved pinned collections when providers are selected.
- `docs/aap-workflow.md`: workflow mapping, evidence contract, failure routing and open decisions.

`skeleton_mode` defaults to true. Setting it false intentionally fails before any
integration placeholder. Implement and validate real adapters and completion checks
before replacing this guard; simply removing it does not create a live workflow.
The sample supports explicit disk/snapshot dispositions and shared-resource exclusions.
Adapters must compare them with discovered attachments, validate every item, preserve
retained storage before provider deletion, and fail on unapproved resources.

Keep approvals and evidence in authoritative systems. Use AAP credentials or an
approved secret store. Keep host key and TLS verification enabled. No credentials,
private keys, or sensitive data should be placed in logs or workflow artifacts.

No Controller templates, ServiceNow changes, removal operations or live integration
tests are performed by this skeleton.
