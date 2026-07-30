# Kanonik

Governance runtime for compliance work, connected to Grok over MCP.

Kanonik gives your AI a typed compliance model to work in, a tamper-evident
record of everything it does, a server-side Verifier that checks its work, and a
human-approval gate in front of every write. Your AI does the reasoning. Kanonik
makes the result defensible.

## What you can do with it

Ask your AI to work on your compliance program in plain language:

- Draft and revise policies and procedures against a loaded framework
- Assess internal controls and record the evidence behind each judgement
- Find the gaps between what a framework requires and what you actually have
- Build an audit trail an assessor can verify independently
- Produce a Statement of Applicability, or the equivalent artifact for your framework

The framework in scope comes from your workspace. Kanonik runs ISO 27001:2022,
SOC 2, GDPR, NIST CSF 2.0 and HIPAA today. Nothing in the plugin is specific to
any one of them.

## Installing

```
/marketplace
```

Then pick Kanonik. Or from the CLI:

```
grok plugin install kanonik --trust
```

On first use your AI connects to `https://app.kanonik.ai/mcp` and a browser
window opens for sign-in. You will need a Kanonik account; sign up at
[kanonik.ai](https://kanonik.ai).

## What this plugin contains

One file that tells Grok where the Kanonik MCP server lives:

```json
{
  "mcpServers": {
    "kanonik": { "type": "http", "url": "https://app.kanonik.ai/mcp" }
  }
}
```

That is the whole plugin. There is no bundled code, no install script, no hook,
and no local process. Everything runs server-side at Kanonik.

## Permissions and data

- **Network.** One endpoint, `https://app.kanonik.ai/mcp`. Nothing else.
- **Authentication.** OAuth 2.1 with Dynamic Client Registration and PKCE,
  against `https://auth.kanonik.ai`. Tokens are held by your Grok client. The
  plugin holds no credential and ships no secret.
- **Scopes.** `kanonik:read` to read your compliance record, `kanonik:propose`
  to draft changes, `kanonik:commit` to submit them for approval.
- **Writes.** No change reaches your record without a signed single-use approval
  token and a human clicking to approve it. Your AI cannot approve its own work.
- **Your other systems.** Kanonik never holds credentials to your source systems.
  If your AI reads from GitHub, Jira, or your identity provider, it does so
  through its own connections, not through Kanonik.

## Links

- Site: [kanonik.ai](https://kanonik.ai)
- Documentation: [docs.kanonik.ai](https://docs.kanonik.ai)
- Support: [kanonik.ai/support](https://kanonik.ai/support)

## License

See [LICENSE](LICENSE). The Kanonik service itself is proprietary; this
repository contains only the connection manifest and documentation for it.
