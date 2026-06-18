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

**macOS** (Intel or Apple Silicon) or **Linux** (x64 or arm64). One command:

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

**Platform notes:**

- **Windows** — there's no native Windows build yet. Install
  [WSL2](https://learn.microsoft.com/windows/wsl/install) (a Linux environment for
  Windows), then run the same command from your WSL shell — it installs the Linux
  build.
- **Linux** — the binaries target glibc. Lightweight musl-based distros (Alpine,
  and some minimal Docker images) aren't supported; on those the binary won't
  start. Use a glibc distro such as Debian, Ubuntu, Fedora, or Arch.

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

From here on, just converse with your agent from inside that folder. Everything
the agent does is a git commit, so nothing is ever silently lost — you can review
or roll back any change ("undo that last change").

---

## 4. Bring in your existing resume

You don't start from a blank page. Point the agent at whatever you already have
and ask it to import:

> *"Import my resume — it's at ~/Downloads/resume.pdf."*
> *"Here's my LinkedIn data export, import it."*

What works as a source:

- **A PDF or Word doc** of your current resume — the agent reads the text and files
  it into structured entries (work, education, skills, …), then you review.
- **Your LinkedIn data export** — the ZIP you get from LinkedIn's *Settings → Get a
  copy of your data*. This imports deterministically (no guessing).
- **A JSON Resume file** (if you happen to have one) — imported directly.

After importing, ask the agent to "show me what you imported" or open a preview
(see [section 6](#6-preview-it-in-your-browser)) and fix anything that didn't land
right ("my title at Acme should be Staff Engineer", "drop the second bullet on the
Globex role").

---

## 5. What you can do once you're set up

Just talk to your agent from inside your resume folder. Some of the most useful
things to ask:

### Edit and add

> *"Add my new role: Staff Engineer at Acme, started March 2025."*
> *"Tighten my summary and move the open-source project above the side gig."*

Each change is committed automatically, with full history.

### Keep different versions of your resume (branches)

Your resume can have variants, and the tool keeps them cleanly separated as git
branches. There are two kinds, and the agent handles the mechanics:

- **`main`** — your canonical resume. The source of truth. When you make a general
  improvement here, the agent can merge it *down* into every variant.
- **Role variants (`role/…`)** — a reusable resume positioned for a *category* of
  role, not one specific job. Ask: *"Make a version of my resume positioned for
  engineering-leadership roles."* The agent creates a `role/engineering-leadership`
  branch (mainly a different title + summary, same work history) that you reuse
  across many applications.
- **Tailored variants (`tailor/…`)** — a resume tailored to **one specific job
  posting**, tied to that job in the tracker (next section).

You can ask to see or export any version: *"Export my role/engineering-leadership
resume as a PDF."*

### Track and tailor for jobs

The tool has a built-in job tracker (a board you view in the browser — see the next
section). Things to ask:

> *"Track this job posting"* (paste the listing or give a link) — the agent adds it
> to your board.
> *"Tailor my resume to this posting and export a PDF."* — the agent branches a
> `tailor/<job>` variant from the closest fit, makes the tailoring edits there, links
> it to the job, and renders it.
> *"How well do I match this job?"* — runs a match analysis and scores your fit; the
> board flags a tailored variant that scores below your base resume.
> *"Move the Acme job to 'interviewing' and add a note about the recruiter call."*

### Export

> *"Show me my resume as a PDF."* (or HTML, or DOCX)
> *"Use the compact theme."*

The first PDF render downloads a rendering engine (see [Good to
know](#7-good-to-know)).

---

## 6. Preview it in your browser

There's a live preview server that shows your resume (in any theme or branch) and
your **job board**, and reloads as changes are made.

Easiest — just ask:

> *"Open a live preview."*  /  *"Open my job board."*

Or run it yourself from inside your resume folder:

```bash
resume preview
```

It opens `http://127.0.0.1:4444` in your browser. The home page lists your themes
and resume variants; add `/jobs` to the URL (`http://127.0.0.1:4444/jobs`) for the
job tracker board — where you can see every tracked job, its status, your match
score, and which resume variant is linked, and edit jobs right there. Stop the
server with `Ctrl+C`. (Useful flags: `--port <n>`, `--no-open`, `--theme <name>`.)

---

## 7. Good to know

- **Your data is yours.** It's a normal git folder. Back it up, push it to your
  own private GitHub, copy it between machines — it's just files.
- **PDF export** downloads a headless Chrome (~170 MB) into `~/.cache/puppeteer/`
  the first time you render. After that it's instant and offline.
- **Themes:** the binary includes two built-in resume styles (`default` and
  `compact`). Ask your agent to "list themes" or "use the compact theme."
- **Reference, anytime:** `resume --help` for the command list, or `resume
  cheatsheet` for the full usage doc (this is also what your agent reads).

---

## Free and Pro

`resume-as-code` is **free to download and use** — nothing expires and there's no
trial clock. Free covers one resume and a small job search. **Pro** lifts the
limits for an active search.

| | Free | Pro |
|---|---|---|
| Resume editing (all sections), import, history, preview, agent skill | Full | Full |
| PDF + HTML export | Yes, with a discreet footer watermark | Yes, no watermark |
| DOCX export; PDF page-size control | — | Yes |
| Section / audience filtering on export | — | Yes |
| Jobs tracked | Up to 5 | Unlimited |
| Tailored resume variants | 1 | Unlimited |
| Match / keyword analysis | 3 total, score only | Unlimited, full breakdown |

When you reach a Free limit the tool tells you exactly which one and how to lift
it — it never crashes or hides your data.

### Get Pro

Pricing is **$12/month, $79/year, or $99 once** (lifetime), handled through Polar.
Buy and manage your plan at:

> **https://resume-as-code.ai/pro**

After purchase you receive a license key. Activate it:

```bash
resume license activate <your-license-key>
resume license status            # should show: pro
```

Activation is verified **offline** — once activated, Pro keeps working with no
network connection. `resume license deactivate` reverts a machine to Free, and a
reinstall keeps your license.

## Uninstall

```bash
curl -fsSL https://github.com/yevgetman/resume-as-code-dist/releases/latest/download/uninstall.sh | sh
```

This removes the binary. It leaves your resume folder(s) and the agent skill
alone — delete those by hand if you want a fully clean slate.
