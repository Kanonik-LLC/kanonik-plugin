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

Claude Code:

```bash
claude mcp add --transport http kanonik https://app.kanonik.ai/mcp
```

Any MCP client that supports streamable HTTP and OAuth sign-in works.
Nothing to install, no API key to paste.

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

**Expect:** four scopes named there, `kanonik:read`, `kanonik:propose`,
`kanonik:commit` and `offline_access` (keeps your client signed in), then a
successful return to your client.

## Step 3: orient

Ask your AI:

> What is my Kanonik workspace set up for?

**Expect:** it calls `kanonik_whoami`, tells you which workspace you are
in, and notes the company profile is not set up. It should then ask about
your organisation one question at a time rather than pasting a form.

Committing is gated until that profile exists, because uncalibrated
compliance output is worse than none.

## Step 4: set up the programme

Answer its questions about your company (industry, size, how far along
your security program is). Any plausible answers work; this is a sandbox. It
does not ask for a framework: every control is placed against NIST CSF 2.0,
which comes with every plan.

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

- **Network:** the plugin configures one MCP endpoint,
  `https://app.kanonik.ai/mcp`, and ships no telemetry or analytics. Sign-in
  is at `https://auth.kanonik.ai`; approvals happen in the Kanonik web app.
- **Credentials:** none held by the plugin. Tokens live in your client.
- **The systems your AI reads:** Kanonik never holds credentials to your
  code, cloud or identity provider. Your AI reads them through its own
  connections, not through us.

More at [kanonik.ai/docs/product/security](https://kanonik.ai/docs/product/security/).

## If something goes wrong

Tell us what you saw. Reaching a broken state is more useful to us than a
clean pass. Email <evereshagin@kanonik.ai>.
