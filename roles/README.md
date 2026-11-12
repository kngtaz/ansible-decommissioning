# Integration roles to implement

- target_validation: authoritative resource identity and ownership checks.
- retirement_readiness: dependency, backup, retention and removal-window checks.
- application_retirement: allowlisted role withdrawal and dedicated endpoint cleanup.
- host_cleanup: evidence preservation, management unregistration and IdM unenrollment.
- compute_removal: provider-specific deletion and retained-storage safeguards.
- enterprise_cleanup: issuing-CA revocation, IdM and Satellite reconciliation.
- network_inventory_retirement: DNS/IPAM cleanup and durable inventory history.
- completion_verification: independent verification and retained-resource reporting.
- servicenow_reporting: work notes, assignments and validation/closure routing.

These are integration boundaries, not implemented roles. Use idempotent operations,
explicit resource identifiers, per-server results, and trusted evidence at each boundary.
Store secrets in AAP credentials or an approved secret store, never workflow artifacts.
