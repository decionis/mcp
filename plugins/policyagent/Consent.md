Decionis PolicyAgent uses your signed-in Decionis account in one workspace.

- `policy:authoring:read` lets this client discover available capabilities and
  read permitted policy revisions, citations, comparisons and workflow status.
- `policy:authoring:propose` additionally lets it store proposed changes when you
  ask. A human author must inspect and accept a proposal into the saved draft.
- `policy:authoring:review-request` additionally lets it request independent review
  of a saved revision when you ask. It cannot record a reviewer's decision.

Policy text and retained citations returned by tools enter the assistant
conversation and are processed under the selected client's terms. Before enabling
access, your workspace must approve the client, purpose, classifications and
processing region for these sources. Account consent alone does not enable it.

Approval, signing and deployment occur in the Decionis workspace. The assistant client's tool
confirmation is permission for that tool call, not a policy attestation. Removing
the OAuth grant, your membership, a policy responsibility or the client approval
prevents subsequent access. Disconnecting does not erase proposals or required
audit records already retained by the workspace.

Never place a client secret, access token or refresh token in a prompt, a plugin
bundle or a checked-in configuration file.

Decision cases use two additional OAuth scopes:

- `decision:read` lets this client list and explain cases the signed-in person may access.
- `decision:write` lets it record that person's explicit decision on a current case.

Case summaries and evidence returned by tools enter the assistant conversation.
The service checks membership, authority and the current case revision, and may
require a Presence passkey. Neither tool confirmation nor a recorded case decision
approves, signs, activates or deploys a policy. Missing case permissions do not
authorize switching to a maker's credentials or another identity.
