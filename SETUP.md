# Setting up Kanonik

Guidance for Claude when a user installs or activates this plugin.

Kanonik is a hosted service. This plugin contains no code: it points your
client at the remote MCP server at `https://app.kanonik.ai/mcp`. Everything
runs server side.

## What the user needs before the plugin does anything

A Kanonik workspace. There is no anonymous or read-only mode, so the first
tool call will fail until the user has signed in.

Two ways to get one:

1. **Sign up** at <https://kanonik.ai/signup>. Kanonik is a paid product and
   signup asks for a card.
2. **Redeem an invite code**, which creates a workspace at no cost and skips
   the card step. Directory reviewers and evaluators should email
   <evereshagin@kanonik.ai> and say what they are evaluating.

## Connecting

No API key, token, or environment variable is needed, and the user should
never be asked to paste one.

1. The client discovers the server and reports that it needs authorisation.
2. A browser opens on `https://auth.kanonik.ai`. The user signs in there.
3. The client receives its token and the connection is live.

Authentication is OAuth 2.1 with PKCE. Clients that support Dynamic Client
Registration, including Claude Code and Claude Desktop, register themselves
and need no client ID. The requested scopes are `kanonik:read` (read the
user's compliance record), `kanonik:propose` (draft changes for review) and
`kanonik:commit` (submit a change for human approval).

## Confirming it worked

Call `kanonik_whoami`. It returns the active workspace, the framework or
frameworks in scope, and the user's role. If it returns a workspace name,
setup is complete.

Do not report the connection as working until that call has returned.

## What to tell the user about writes

Nothing this plugin does reaches the user's record silently. Every write runs
through a server-side Verifier and then stops at a single-use approval link
that a human has to click. The connected model cannot approve its own work.
That is the product's central guarantee, and it is worth stating once during
setup so the user knows what to expect the first time a commit is proposed.

## If a first run fails

- **Sign-in succeeds but tool calls are rejected.** The workspace exists but
  the session is stale. Reconnect the server so a fresh token is issued.
- **The user has no workspace.** Point them at signup or the invite code
  route above. Do not try to work around it; there is no unauthenticated mode.
- **A commit appears to hang.** It is waiting on the human approval click,
  which is by design. Check for a pending approval rather than retrying.
