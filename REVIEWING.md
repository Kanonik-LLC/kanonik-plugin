# Reviewing this plugin

For directory and marketplace reviewers assessing Kanonik. Written to be
followed cold by someone who has never seen the product. Budget about 15
minutes.

## Getting access

Kanonik is a paid product and signup asks for a card, so reviewing it
needs a sandbox account that skips that.

**Email <evereshagin@kanonik.ai>** and we will send you a sandbox invite
code. Say which directory you are reviewing for. The code lets you create
your own isolated workspace at no cost.

We deliberately do not publish credentials here. If you would rather not
create an account at all, ask and we will supply a shared sandbox login
instead.

The sandbox starts **empty on purpose**. You are walking the real
first-run path, not touring a pre-filled demo.

## What this plugin is

One file pointing your client at our hosted MCP server:

```json
{
  "mcpServers": {
    "kanonik": { "type": "http", "url": "https://app.kanonik.ai/mcp" }
  }
}
```

No bundled code, no install script, no hooks, no local process. Kanonik
itself runs server-side.

## Step 1: connect

Grok Build:

```bash
grok plugin install kanonik --trust
```

Claude Code or Claude Desktop:

```bash
claude mcp add --transport http kanonik https://app.kanonik.ai/mcp
```

Any MCP client speaking streamable HTTP works. Nothing to install, no API
key to paste.

**Expect:** your client discovers the server and reports it needs
authorisation.

## Step 2: sign in

Your client opens a browser. Sign in, or redeem the invite code to create
your workspace.

This is OAuth 2.1 with Dynamic Client Registration and PKCE. Your client
registers itself; nobody pre-provisions a client ID for you. The server
publishes its own metadata if you want to inspect it before trusting it:

```bash
curl https://app.kanonik.ai/.well-known/oauth-protected-resource
```

**Expect:** three scopes named there, `kanonik:read`, `kanonik:propose`
and `kanonik:commit`, then a successful return to your client.

## Step 3: orient

Ask your AI:

> What is my Kanonik workspace set up for?

**Expect:** it calls `kanonik_whoami`, tells you which workspace you are
in, and notes the company profile is not set up. It should then ask about
your organisation one question at a time rather than pasting a form.

Committing is gated until that profile exists, because uncalibrated
compliance output is worse than none.

## Step 4: set up the programme

Answer its questions. Any plausible answers work; this is a sandbox. When
it asks which framework you are pursuing, say:

> NIST CSF 2.0

**Why that one:** five framework packages are available (ISO 27001:2022,
SOC 2, GDPR, NIST CSF 2.0, HIPAA). ISO 27001 and SOC 2 require you to
attest you hold your own licensed copy of the standard text, because we
do not redistribute it. NIST CSF 2.0 is US federal public domain and has
no such gate, so it keeps this review on the path you came to assess. If
you want to see the licence attestation, pick ISO 27001:2022 instead.

## Step 5: do a piece of real work

> Draft an access control policy for us and record it.

**Expect, in order:**

1. It reads the framework requirements.
2. It drafts a policy and proposes it.
3. A server-side **Verifier** returns a verdict. You did not invoke it and
   your AI cannot skip it.
4. The commit returns an **approval URL**, not a confirmation.

Your AI cannot approve its own work. If it claims the policy is committed
before you have clicked anything, that is a defect and we want to hear
about it.

## Step 6: approve

Open the approval URL. You will see what is about to be written, the
Verifier's verdict, and its reasoning. Click approve.

**Expect:** the write lands and the token is single-use. Reload the URL
and it is spent.

## Step 7: check the record

> Show me what was recorded and how it was verified.

**Expect:** the sealed entry with its Verifier verdict and approval
evidence, chained to the entry before it.

## What you should have seen

- Connected with no API key and no manual client registration
- Committing gated until the programme was calibrated
- A Verifier verdict you did not ask for and could not bypass
- A write that required your click, on a single-use token
- A queryable record of what happened and why

## Checking our claims without trusting us

Every audit export carries its own verifier, and that verifier is
published separately so you never have to trust the copy inside the
bundle you were handed:

```bash
git clone https://github.com/Kanonik-LLC/kanonik-verify.git
python3 kanonik-verify/verify.py <bundle>
```

MIT licensed, no network calls, exit code 0 means every check passed. You
can diff that repository against the `chain/` directory in any bundle; a
difference is itself a finding.

## Security questions

- **Network:** one endpoint, `https://app.kanonik.ai/mcp`. No telemetry,
  no analytics, no other hosts.
- **Credentials:** none held by the plugin. Tokens live in your client.
- **Your other systems:** Kanonik never holds credentials to them. If
  your AI reads from GitHub or your identity provider, it does so through
  its own connections, not through us.

More at [kanonik.ai/docs/product/security](https://kanonik.ai/docs/product/security/).

## If something goes wrong

Tell us what you saw. Reaching a broken state is more useful to us than a
clean pass. Email <evereshagin@kanonik.ai>.
