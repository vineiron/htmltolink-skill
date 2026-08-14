# htmltolink skill

Publish an HTML file and get a public link — from inside your AI coding agent.

When you (or your agent) have a self-contained HTML file and want to share it,
this skill tells the agent to publish it to [htmltolink.com](https://htmltolink.com)
and hand you back a public link plus a private manage link. No account, no signup.

## Install

```sh
npx skills add vineiron/htmltolink
```

Works with Claude Code, Cursor, Codex, Copilot, and other agents supported by the
[skills CLI](https://skills.sh).

## Use

Just ask your agent to share the page, in your own words:

> "share this HTML file"
> "put this page online and give me a link"
> "make this a link I can send to someone"

The agent publishes it and shows you:

- **Public link** — share with anyone (`something.htmltolink.site`).
- **Manage link** — keep private; update or delete the page from it.

## How it works

The skill is a single `curl` against htmltolink's public API:

```sh
curl -sS -X POST https://htmltolink.com/v1/pages \
  -H 'Content-Type: text/html' --data-binary @index.html
```

Everything is auditable in [`SKILL.md`](./SKILL.md) — no hidden network calls, no
credentials. One self-contained HTML file per page; anonymous uploads up to 10 MB
(publish from the website for 25 MB).

## Links

- Website: https://htmltolink.com
- Terms: https://htmltolink.com/terms · Privacy: https://htmltolink.com/privacy

## License

MIT
