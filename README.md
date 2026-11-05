# Arcla — Claude plugin

Connect Claude to your Arcla calls and knowledge base. Claude can find and
plan your calls, read their transcripts, prepare notes and outlines, and
search and maintain your organization's knowledge base. Connecting is the
URL and nothing else: Arcla runs its own OAuth authorization server, so
there is no client id to be handed out of band and no API key to paste or
store.

## Install (Claude Code)

    claude plugin marketplace add frontierxai/claude-plugin
    claude plugin install arcla@arcla

The plugin adds the Arcla MCP server to Claude Code but does not sign you
in. Run `/mcp` and select `arcla`: your browser opens, you sign in with
Arcla, pick the organization, and approve. That is the whole flow — no
client id, no key. Tokens live in your OS credential store and refresh
automatically.

To disconnect, run `/mcp`, select `arcla` and disconnect — that removes the
tokens from this machine. A self-serve connected-apps page where you can
revoke the grant itself is not built yet (FRO-5128); until it ships, ask
Arcla support to revoke it and an operator will do so.

## Without the plugin

Adding the URL is the entire setup:

    claude mcp add --transport http arcla https://mcp.arcla.com/mcp/v1

In Claude Desktop or claude.ai, add a custom connector with that same URL.
Nothing goes in "Advanced settings".

## Staging (Arcla team)

    claude mcp add --transport http arcla-stg https://mcp-stg.arcla.com/mcp/v1

## Multiple organizations

You pick the organization on Arcla's own consent page when you connect, and
the connection is bound to the org you choose. If you belong to a single org
there is nothing to choose.

To use a different organization, reconnect: `/mcp`, disconnect `arcla`,
then connect again and pick the other org.

## Skills

The plugin also carries Arcla's chat skills, so Claude knows how to do a
whole job with the tools rather than improvising one from their
descriptions. Claude picks a skill from what you say; there is nothing to
invoke.

- **Prepare me for this call** (`call-prep`): catches up on everything on
  record for your next call, then gives a short brief: what we know, with
  where each fact came from, and what is still unknown.
- **How did I do on this call?** (`call-review`): feedback on a call that
  has started or ended, each point with a quote from the call.
- **Update the CRM** (`crm-update`): reads a call and proposes changes to
  the CRM records it touched. Needs a CRM connection in your assistant
  (for example a HubSpot or Salesforce connector).
- **Save this for the team** (`save-for-team`): turns something from a call
  into an FAQ, an objection or a competitor point for your team. Members
  propose it for an admin to approve; admins save it directly. Nothing is
  saved until you have seen the exact text.

The files under `plugin/skills/<skill-id>/SKILL.md` are **generated, never
edited by hand**: each is the released text of the same skill the Arcla
desktop chat runs, with a short preamble for assistants that do not have a
call open on screen. They are written by
`bun run --filter @frontierx/call-server docs:plugin-skills` in the Arcla
monorepo, and a test there fails when the files and the skill text disagree.
An edit here is lost on the next run; change the skill text instead.

## Tools

Every call tool is scoped to **your own calls**, org admins included —
colleagues' calls and transcripts are not reachable through this connection.
The knowledge base, competitors, objections and call outlines are shared
across the organization by design.

Tools that change the knowledge base, competitors or objections need an org
admin; members can propose additions instead. A few tools are switched on
per environment, so your connection may list fewer than the table below.

<!-- mcp-tools:start (generated from MCP_TOOLS; run `bun run --filter @frontierx/call-server docs:plugin-readme`) -->

| Tool | What it does | Reaches |
|---|---|---|
| `list_calls` | Find your calls — upcoming, live or already finished. | Your calls |
| `get_call` | Look up one of your calls, including the prep notes you see. | Your calls |
| `list_calendar_events` | See the meetings on your connected calendar, and who is invited to them. | Your calendar |
| `plan_call` | Plan a call Arcla will help with, from a calendar meeting or just a title, so it shows on your prep screen. | Your calls |
| `get_transcript` | Read back what was said on one of your calls, live or finished. | Your calls |
| `search_knowledge` | Ask a question and get an answer from your team's knowledge base. | Your organisation |
| `update_call_notes` | Add to, or rewrite, the prep notes for one of your calls. | Your calls |
| `get_call_notes` | Read the prep notes for one of your calls, one paragraph or bullet at a time. | Your calls |
| `edit_call_notes` | Change, remove or add individual lines in the prep notes for one of your calls. | Your calls |
| `get_call_outline` | Read the outline you follow during a call, and how far through it you got. | Your calls |
| `set_call_outline` | Write the outline you will follow during an upcoming call. | Your calls |
| `list_knowledge` | Browse what is in your team's knowledge base, and where each item came from. | Your organisation |
| `get_knowledge_item` | Read one document, fact or answer from your knowledge base. | Your organisation |
| `list_outlines` | See your team's call outlines. | Your organisation |
| `list_related_calls` | See your team's earlier calls with the same people or company: summaries and objectives, never transcripts. | Your organisation |
| `get_outline` | Read one call outline — its sections, talking points and objectives. | Your organisation |
| `update_call` | Change the title, time or attendees of one of your upcoming calls, or record what we know about the prospect. | Your calls |
| `delete_call` | Remove one of your calls that has not started yet. | Your calls |
| `assign_call_outline` | Pick one of your team's call outlines for an upcoming call. | Your calls |
| `list_competitors` | See the competitors your team tracks. | Your organisation |
| `get_competitor` | Read one competitor — its profile and the talking points against it. | Your organisation |
| `create_competitor` | Add a competitor to your team's list (admins only). | Your organisation |
| `update_competitor` | Change a competitor's name, website or notes (admins only). | Your organisation |
| `delete_competitor` | Remove a competitor from your team's list (admins only). | Your organisation |
| `set_competitor_intelligence` | Store researched background on a competitor for admins to read (admins only). | Your organisation |
| `create_competitor_point` | Add a talking point against a competitor (admins only). | Your organisation |
| `list_objections` | See your team's objection handling. | Your organisation |
| `get_objection` | Read one objection and how to answer it. | Your organisation |
| `create_objection` | Add an objection and how to answer it (admins only). | Your organisation |
| `create_guidance` | Add a coaching note for reps (admins only). | Your organisation |
| `update_assistance_entry` | Change a talking point or objection (admins only). | Your organisation |
| `delete_assistance_entry` | Remove a talking point or objection (admins only). | Your organisation |
| `restore_assistance_entry` | Bring back a removed talking point or objection (admins only). | Your organisation |
| `create_knowledge_item` | Add an FAQ or a fact to your knowledge base, live on calls straight away (admins only). | Your organisation |
| `update_knowledge_item` | Change an FAQ or a fact in your knowledge base (admins only). | Your organisation |
| `delete_knowledge_item` | Remove an FAQ, a fact, an uploaded document or a transcription vocabulary term from your knowledge base (admins only). | Your organisation |
| `restore_knowledge_item` | Bring back a removed FAQ, fact or document (admins only). | Your organisation |
| `set_knowledge_visibility` | Put an FAQ or a fact on calls, or take it off them (admins only). | Your organisation |
| `set_vocabulary_status` | Approve or dismiss a suggested transcription vocabulary term (admins only). | Your organisation |
| `propose_knowledge_item` | Suggest an FAQ or fact for an admin to review before it is used. | Your organisation |
| `propose_objection` | Suggest how to handle an objection, for an admin to review. | Your organisation |
| `propose_competitor_point` | Suggest a competitor talking point, for an admin to review. | Your organisation |
| `get_call_objectives` | List what you need to come away from a call with. | Your calls |
| `set_call_objectives` | Set what you need to come away from a call with. | Your calls |
| `whoami` | Show who you are signed in as and which organisation you act in. | Your calls |
| `export_knowledge` | Export your whole knowledge base, drafts included, for copying (admins only). | Your organisation |
| `apply_knowledge_batch` | Apply a copied set of knowledge base items, with a dry run first (admins only). | Your organisation |
| `get_org_overview` | Read the organisation's approved company overview (admins only). | Your organisation |
| `set_org_overview` | Replace the organisation's approved company overview, with a dry run first (admins only). | Your organisation |
| `export_document_file` | Get a download link and the extracted text for one uploaded document, for copying it (admins only). | Your organisation |
| `import_document_file` | Copy an uploaded document into your knowledge base, file and all (admins only). | Your organisation |
| `start_website_crawl` | Start a crawl of a website for your organisation (admins only). | Your organisation |
| `delete_crawled_site` | Delete a crawled website and its pages from your organisation (admins only). | Your organisation |
| `list_crm_writes` | See the changes Arcla made in your connected CRM, newest first (admins only). | Your organisation |
| `undo_crm_write` | Put one change Arcla made in your connected CRM back to what it was (admins only). | Your organisation |
| `sync_crm_fields` | Refresh Arcla's record of your connected CRM's custom fields and dropdown options (admins only). | Your organisation |
| `survey_crm_connector` | Record how your team uses your connected CRM: fields filled, stages and lists, as counts only (admins only). | Your organisation |
| `list_crm_fields` | See the custom fields and dropdown options Arcla knows of in your connected CRM (admins only). | Your organisation |
| `list_crm_facts` | See what Arcla has learned about how your connected CRM is used (admins only). | Your organisation |
| `retire_crm_fact` | Retire one thing Arcla learned about your connected CRM so it is no longer used (admins only). | Your organisation |

<!-- mcp-tools:end -->

## Try it

- *"List my calls from today, then turn the latest one into a set of tickets."*
- *"Plan my next meeting with Acme and draft an outline from our last call."*
- *"Search the knowledge base for our refund policy and summarize it."*
