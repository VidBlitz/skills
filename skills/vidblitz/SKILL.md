---
name: vidblitz
description: Make faceless YouTube videos (documentaries, explainers, top-10 lists, story videos) with Vidblitz through its MCP tools, from a topic or from a script written with the user. Use when the user wants a YouTube video made from an idea or a script, wants their script turned into a narrated, edited video, wants to check on their Vidblitz videos or download them, asks about their Vidblitz credits, or mentions Vidblitz.
---

# Vidblitz

Vidblitz makes long-form faceless YouTube videos: it researches the topic (or takes the user's script word for word), narrates it, finds and edits footage, animated scenes and music, and renders the video with its thumbnails, title ideas, description and chapters. A ten-minute video takes one to three hours to produce.

## The one rule: quote, tell, wait for the go

Creating a video and reading its quote are free. **`confirm_video` spends the user's credits.** Always:

1. Wait for the project to reach `awaiting_confirmation` and read its `quote`.
2. Tell the user the exact `quote.credits`, the `quote.duration_seconds` (as minutes), their balance from `get_account`, and **every** warning in `quote.warnings` (its `summary` and `how_to_fix`).
3. Call `confirm_video` only after they say yes to that quote, with `quote.quote_id` and **all** the warning codes in `acknowledged_warnings`.

Never confirm on your own initiative, never confirm a quote the user hasn't seen, and never shorten or change a request to make it cheaper without asking.

## Use the Vidblitz tools

When Vidblitz is connected (as a connector in ChatGPT or Claude, a plugin in Claude Code, or an MCP server in Cursor or VS Code), its tools are already available: `get_account`, `create_video`, `get_project` and the rest. Use them.

If they aren't available, the user hasn't connected Vidblitz yet. Ask them to add the MCP server `https://vidblitz.ai/mcp` and sign in with their Vidblitz account (setup for each app: https://vidblitz.ai/developers/mcp). Don't switch to the REST API instead unless you are writing code for them; then see [references/rest.md](references/rest.md).

Apps that draw cards (MCP Apps) show `get_project` as a Vidblitz card: while the video waits for confirmation its quote is an order ticket with a **Make the video** button. Tapping it is the user's yes and the card confirms the video itself, so **don't call `confirm_video` again** after they tap it (their message or the card's note says so). `show_projects` draws their recent videos to pick from.

## Make a video

1. **Check the account**: `get_account`. Note the workspace name, `plan.active` and `credits.available`. Making videos needs an active plan: without one, tell the user to choose a plan in the Vidblitz app (Settings, Billing) and stop. `plan.concurrent_videos` is how many it may produce at once.
2. **Agree on what to make** (see [Prompt or script](#prompt-or-script)). Ask only what matters: the topic or script, the audience, the length, and the quality tier if they care about cost. `get_options` lists the tiers with `credits_per_minute`, the languages and the length limits. `list_brand_profiles` lists saved voices, languages and looks; offer one if they have any.
3. **Create**: `create_video` with `prompt` and/or `custom_script`, `tier` (`low`, `medium` by default, `high`), and optionally `brand_profile_id`, `content_reference_url` (a YouTube video whose story or facts it draws on) and `thumbnail_reference_url`. Send an `idempotency_key` you make up for this video, so a retry can't start a second one. It answers with a `request_id`.
4. **Wait for the check**: `wait_for_video` with the `request_id` (or `get_video_request`). It ends at:
   - `ready`: `project_id` is the new project. Go on.
   - `blocked`: `issues` say what to change. Tell the user, fix the request with them, and `create_video` again.
   - `check_unavailable` / `preflight_unavailable`: Vidblitz couldn't check it right now. Try again later.
   Warnings in `issues` don't stop the video; they come back in the quote.
5. **Wait for the quote**: `wait_for_video` with the `project_id` until `stage` is `awaiting_confirmation` (a few minutes: Vidblitz plans the video to price it).
6. **Quote, tell, wait for the go** (above), then `confirm_video`. A `stale_quote` (409) means the quote was replaced: read it again with `get_project` and tell the user the new one.
7. **Check back**: `wait_for_video` waits up to 55 seconds per call. While `stage` is `in_production`, `progress_percent` and `step` (its `name` and `detail`) say how far along it is. Production takes hours, so give the user an update and tell them they can come back later and ask you to check, rather than calling it in a loop for long.
8. **Deliver**: at `completed`, share from `outputs` the rendered video's `download_url`, the thumbnails (each in `variants[].download_url`), the title ideas, description and chapters (`text`), and `app_url`, where the video can be edited in the browser. Download links last six hours: `get_project` gives fresh ones.

## Prompt or script

- **A topic** goes in `prompt`: what it's about, its angle, audience, tone and length, 50 to 10,000 characters. Vidblitz researches and writes the script. Start it with a `Title: …` line to name the project.
- **A script** goes in `custom_script`, 200 to 60,000 characters. It is narrated **word for word** and the video's length follows it. When you write a script with the user, pass it exactly as agreed: don't paraphrase it into a prompt, and don't add stage directions, headings or notes the narrator would read out. A `prompt` beside it can steer the visuals and tone.
- Roughly 150 narrated words make a minute of video. Tell the user the length a script will give before creating it.

## Needs review

`stage` `needs_review` means some shots couldn't be filled. `review.placeholders` lists each one (`slot_id` and `reason`) and `review.how_to_finish` says what to do: a person opens `app_url`, adds an image or video for each placeholder in the editor (or removes it) and finishes the video there. Placeholders are never rendered. Tell the user which shots need them and why, and send them the link; you can't finish it for them.

## When something goes wrong

- **`plan_required`** (402): no active plan. Tell the user to choose one in the app; don't retry.
- **`concurrent_generation_limit`** (429): the workspace already produces as many videos as its plan allows. Wait for one to finish, or tell the user.
- **`invalid_request`** (400): `issues` name the fields. Fix and retry.
- **`failed`**: `failure` names the step that failed and its code. Unspent reserved credits are released. Tell the user, and send them `app_url` to contact support from the project. (`retry_video` exists but is for Vidblitz staff; others get 403.)
- **Cancel or delete** only when the user asks: `cancel_project` stops a video being checked or produced and can't be resumed; `delete_project` removes a project and its files for good. Confirm first.
- **Workspaces**: one connection acts for one workspace (`get_account` names it). To use another, the user reconnects and picks it on the consent screen.
- **New values**: new stages and error codes can appear. Treat an unknown stage as "check again later" and relay `error` to the user.

Full reference, every tool with its fields: https://vidblitz.ai/developers/llms.txt
