# AAP decommissioning workflow outline

This is a design outline, not an importable Controller export. No templates are created.
ServiceNow owns request intake, service-owner approval, infrastructure approval,
clarification/denial loops and final acceptance. Launch AAP only after approval.
Caller-supplied booleans or sample evidence strings are not proof of authorization.

Create an SCM Project, localhost orchestration inventory and approved execution
environment. Create one Job Template per playbook. Supply the approved `request`
payload at workflow launch and attach appropriate AAP credentials.

| Job | Playbook | Success | Failure |
|---|---|---|---|
| 1 | `playbooks/01_validate_targets.yml` | Job 2 | Failure callback |
| 2 | `playbooks/02_confirm_retirement_readiness.yml` | Job 3 | Failure callback |
| 3 | `playbooks/03_withdraw_application_services.yml` | Job 4 | Failure callback |
| 4 | `playbooks/04_cleanup_host.yml` | Job 5 | Failure callback |
| 5 | `playbooks/05_remove_compute.yml` | Job 6 | Failure callback |
| 6 | `playbooks/06_retire_enterprise_records.yml` | Job 7 | Failure callback |
| 7 | `playbooks/07_release_network_inventory.yml` | Job 8 | Failure callback |
| 8 | `playbooks/08_verify_completion.yml` | Requester validation | Failure callback |

Use `playbooks/90_handle_failure.yml` as the starting point for failure branches.
Route target mismatches to infrastructure admin; readiness dependency or data-disposition
decisions to service owner; technical readiness and later execution failures to
infrastructure admin. An expired window requires rescheduling, not deletion.
Keep the ticket open on any incomplete required action. A successful failure callback
must not be interpreted as successful decommissioning; assess all execution nodes.

## Evidence and cross-job data

Implement durable per-server evidence in ServiceNow or the approved evidence store.
Capture resource IDs, DNS records, certificate issuer/serial references, IdM records,
Satellite IDs, disk/snapshot IDs and ownership before compute removal. Publish only
nonsecret resource/evidence references through `ansible.builtin.set_stats` with
`per_host: false`. The skeleton publishes only `decommissioning_preview`, never a
fabricated completed result. Rebuild connection inventory in each host-side job;
`add_host` and local facts alone do not persist between separate AAP jobs.

Job 5 must verify absence and storage disposition before downstream cleanup. Initial
implementation should stop the batch on any unresolved failure. If partial-batch
continuation is later enabled, each server must independently satisfy every gate.
Do not infer absence from API errors. Handle already-absent resources only after
identity reconciliation and corroboration with prior evidence.

## Implementation decisions

Confirm cloud provider, VMware API scope, actual Purview product/API, authoritative
DNS/IPAM ownership, ServiceNow table/states, credential models, retained-volume
behavior, certificate issuers, host access after IdM unenrollment, backup/retention
policy, removal-window enforcement and safe retry boundaries. The readiness stage
checks the window and destructive adapters must recheck it before execution.
Never automatically recreate removed servers or delete retained storage as rollback.

Reference for nonsecret artifacts:
https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/set_stats_module.html
