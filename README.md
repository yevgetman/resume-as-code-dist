# resume-as-code — user guide

A guide for using `resume-as-code` with an AI coding agent (Claude Code is the
running example). No prior setup knowledge assumed.

## What this is

`resume-as-code` keeps your resume as **version-controlled structured data** — a
folder of small files, one per entry (each job, each school, each project), with
git tracking every change. You don't edit those files by hand. Instead you **talk
to an AI agent**, and the agent runs the `resume` command-line tool for you:

> *"Add my new role: Staff Engineer at Acme, started March 2025."*
> *"Import my old resume"* (point it at a PDF or your LinkedIn export).
> *"Tailor my resume for this job posting and export a PDF."*

You get a clean, structured resume you can render to PDF/HTML, plus a full history
you can roll back — and because it's an agent driving a precise tool, the data
stays consistent.

---

## 1. Install

macOS or Linux. One command:

```bash
curl -fsSL https://github.com/yevgetman/resume-as-code-dist/releases/latest/download/install.sh | sh
```

This installs the `resume` binary to `~/.local/bin/resume`. If the installer says
`~/.local/bin` isn't on your PATH, add the line it prints to your `~/.zshrc` (or
`~/.bashrc`), then open a new terminal. Verify:

```bash
resume --version
```

On macOS the installer also clears the Gatekeeper quarantine flag, so the binary
just runs.

> This is a time-limited build (see [Heads-up](#heads-up) below). When it stops
> working, ask whoever shared it for a fresh copy and re-run the line above.

---

## 2. Set up your agent

Pick the path that matches your agent.

### Claude Code (recommended)

Install the bundled "skill" so Claude Code knows how to drive the tool, then start
a new session:

```bash
resume init-skill
```

That copies the skill into `~/.claude/skills/resume-as-code/`. Open Claude Code
(or start a new conversation) and it will automatically know how to manage your
resume — just talk to it.

### Any other agent (or as a fallback)

Every copy of the tool carries its own instruction manual. Just tell your agent:

> *"There's a `resume` CLI installed. Run `resume cheatsheet` to learn how it
> works, then help me manage my resume."*

The agent runs that once, reads the embedded guide, and is ready to go.

---

## 3. Start your resume

Your resume lives in a folder you own (a git repository). Either ask your agent to
create it, or do it yourself:

```bash
resume init ~/my-resume
cd ~/my-resume
```

From here on, just converse with your agent from inside that folder. A few things
to try:

| You say | What happens |
|---|---|
| "Import my current resume" (give it a PDF/DOCX path, or your LinkedIn data export) | The agent extracts the content and files it into structured entries. |
| "Add my role at Acme as Staff Engineer from March 2025." | A new work entry is created and committed. |
| "Fix the dates on my last job." | The agent edits the entry; the change is committed. |
| "Show me my resume as a PDF." | Renders a PDF (downloads a rendering engine the first time — see below). |
| "Open a live preview." | Starts a local preview server in your browser. |
| "Track this job posting and tailor my resume to it." | Saves the posting, scores your fit, and creates a tailored variant. |
| "Undo that last change." | Rolls back via git history. |

Everything is a git commit, so nothing is ever silently lost — you (or the agent)
can review or revert any change.

---

## 4. Good to know

- **Your data is yours.** It's a normal git folder. Back it up, push it to your
  own private GitHub, copy it between machines — it's just files.
- **PDF export** downloads a headless Chrome (~170 MB) into `~/.cache/puppeteer/`
  the first time you render. After that it's instant and offline.
- **Themes:** the binary includes two built-in resume styles (`default` and
  `compact`). Ask your agent to "list themes" or "use the compact theme."
- **Reference, anytime:** `resume --help` for the command list, or `resume
  cheatsheet` for the full usage doc (this is also what your agent reads).

---

## Heads-up

This is a **time-limited build**. It works for a set period from your first run,
and every build has a hard stop date — whichever comes first. When it expires,
running any command prints `E_RESUME_BUILD_EXPIRED` and stops. That's expected:
just ask whoever shared it with you for a fresh build and re-run the install
command from step 1.

## Uninstall

```bash
curl -fsSL https://github.com/yevgetman/resume-as-code-dist/releases/latest/download/uninstall.sh | sh
```

This removes the binary. It leaves your resume folder(s) and the agent skill
alone — delete those by hand if you want a fully clean slate.
