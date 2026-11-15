Decionis for Claude — policies and decision cases
================================================

One Decionis plugin provides policy-authoring and decision-cases skills through
the existing remote MCP endpoint. It requires an approved Decionis workspace, the
three authoring OAuth scopes and decision:read / decision:write listed in Consent.md.

For Claude web and Desktop/Cowork, import the bundle into your permitted plugin
marketplace or use the reviewed directory entry once published. The directory
submission reads the plugin files from the public `decionis/mcp` repository;
building the ZIP alone does not submit or publish it. Connect with the
registered Decionis OAuth integration, select the intended workspace, and inspect
the requested permissions. Administrators must configure the registered client
credentials in the provider's connection settings, never in this archive. This
bundle does not perform dynamic client registration.

Start with: "Find our sandbox policies and explain the current refund policy
with citations." Then try: "Prepare a proposal to clarify that clause, preserving
the sources." Inspect and accept the proposal in the workspace before asking:
"Request testing review of the saved revision and give me the review link."

For cases, ask: "List the decision cases waiting for me and explain the largest
held payment." Recording a decision requires your explicit instruction for the
exact current case and may require a passkey. It never approves a policy.

For Claude Code, prefer the connected Claude account's existing remote connector
where that surface supports it. Confirm in `/mcp` that both workflows' tools are
present. Do not add a duplicate local HTTP entry that hides the account connector.
The standalone HTTP configuration requires its own compatible OAuth registration;
Decionis currently accepts registered HTTPS callbacks and does not support the
default Code loopback callback or dynamic registration.

An advanced Code/local alternative is the explicitly connected `@decionis/mcp`
0.6.0 package. Its three private environment settings are:

```text
DECIONIS_POLICY_CONNECTED=1
DECIONIS_POLICY_WORKSPACE_ID=<selected Decionis workspace UUID>
DECIONIS_POLICY_OAUTH_TOKEN=<short-lived delegated OAuth access token>
```

Acquire the token through your approved registered OAuth client or credential
broker. Supply it privately to the process; never paste it into chat or save it in
this bundle. The local server re-reads credentials per call and fails closed when
they expire. It does not implement a login or refresh-token store. Start the
installed `decionis-mcp` executable from that environment. Without the opt-in flag,
the additional PolicyAgent tools are absent. Existing local execution hooks keep
their existing behavior.

The local adapter exposes policy-authoring tools only. Decision cases require
the authenticated remote account connector; missing tools must not be replaced
with another person's credentials.

The repository's reviewer instructions include independent author/reviewer
accounts, negative cases and evidence capture. Successful ZIP validation or
local stdio discovery does not establish directory publication or a live OAuth
connection; those checks must be recorded for each supported client surface.

[Privacy](https://decionis.com/privacy) · [Terms](https://decionis.com/terms) ·
[Support](https://decionis.com/contact)
