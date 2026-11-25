# Kanonik

Security compliance software that works inside your AI.

Kanonik makes your AI a compliance expert and checks every draft before you see
it. Only what a named person approves goes into your Record, and anyone can
verify that Record offline, without a Kanonik account.

## What you can do with it

Ask your AI in plain words:

- Draft your security policies and keep them current
- Build your controls, risks, vendors and evidence
- Answer a customer's security questionnaire from what your team approved
- See where you stand on a framework, and what is left to do
- Check a new vendor or a change against what you promised, with Foresight
- Prepare for an audit and send proof the recipient checks for themselves

## Frameworks

SOC 2, ISO/IEC 27001, HIPAA, GDPR, NIST CSF 2.0, NIST SP 800-53 Rev. 5, NIST SP 800-171 Rev. 2 and Rev. 3, FedRAMP Rev. 5 baselines, GovRAMP Rev. 5 baselines, CCPA/CPRA, DORA, NIS2, NYDFS Part 500, EU AI Act, NIST AI RMF 1.0 and NIST SSDF. NIST CSF 2.0 and GDPR come with every plan.

## Connecting

Kanonik is a hosted MCP server at `https://app.kanonik.ai/mcp`. Any MCP client
that speaks streamable HTTP can connect to it. In Claude Code:

```
claude mcp add --transport http kanonik https://app.kanonik.ai/mcp
```

On first use a browser window opens for sign-in. Start your 14-day trial at
[kanonik.ai](https://kanonik.ai).

## What this plugin contains

One file that tells your client where the Kanonik MCP server lives:

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
  against `https://auth.kanonik.ai`. Tokens are held by your client. The plugin
  holds no credential and ships no secret.
- **Scopes.** `kanonik:read` to read your Record, `kanonik:propose` to draft
  changes, `kanonik:commit` to submit them for approval.
- **Writes.** Kanonik checks every draft, and nothing reaches your Record until
  a person approves it. Your AI cannot approve its own work.
- **Your data.** We do not train, fine-tune or evaluate any AI model on your
  data. Kanonik never asks for or stores credentials to your other systems. If
  your AI reads from GitHub, Jira or your identity provider, it does so through
  its own connections.

## Links

- Site: [kanonik.ai](https://kanonik.ai)
- Help center: [kanonik.ai/docs](https://kanonik.ai/docs/)
- Trust: [kanonik.ai/trust](https://kanonik.ai/trust)
- Contact: [kanonik.ai/contact](https://kanonik.ai/contact)

## License

See [LICENSE](LICENSE). The Kanonik service itself is proprietary; this
repository contains only the connection manifest and documentation for it.
