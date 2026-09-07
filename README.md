# kouludata — template

A starting point for your own **school data repository**: the private GitHub repo
that a [Koulu dashboard](https://atkoulu.vercel.app) reads and writes.

The dashboard is one page with your courses, Zoom and Moodle links, timetable,
deadlines and notes. It has no backend and no database. Everything it knows
lives in your browser and, once you finish the setup below, in **a repository
you own** — this one, once you have made your copy of it.

---

## What you end up with

```
       your browser                      your private repo
   ┌───────────────────┐              ┌──────────────────────┐
   │  atkoulu.vercel.  │  GitHub API  │  data.json           │
   │  app              │ ───────────► │  Example course/     │
   │                   │ ◄─────────── │  Statistics/  …      │
   │  your token       │              └──────────────────────┘
   └───────────────────┘
```

- **`data.json`** — courses, weekly slots, imported calendar events, deadlines,
  notes. The dashboard writes it; every save is a commit.
- **Course folders** — your own documents. Browsable, and text files editable,
  from the dashboard's **Files** tab.
- **Your token** — stays in your browser. It is never part of the page, never
  sent anywhere except `api.github.com`, and never committed.

Nobody else can see any of it. The page you open is a shell; the data is yours.

---

## Setup (about five minutes, once)

### 1. Make your own repository

Click **Use this template → Create a new repository** at the top of this page.

Name it whatever you like — `kouludata` is a fine choice. **Set it to Private.**
This repo will contain your timetable, your notes and your coursework.

You now have `data.json` (an empty document), a `.gitignore` that keeps calendar
exports and tokens out, and an example course folder you can delete.

### 2. Create a token

Go to **[github.com/settings/personal-access-tokens](https://github.com/settings/personal-access-tokens)**
→ *Generate new token* → **Fine-grained**.

| Field | Value |
|---|---|
| Token name | `koulu-dashboard` (anything) |
| Expiration | Your call. A year is convenient; shorter is safer. |
| Repository access | **Only select repositories** → the repository you just created |
| Permissions | **Repository permissions → Contents → Read and write**. Nothing else. |

Generate it and copy the value — GitHub shows it once.

> Why this permission: the dashboard writes `data.json` and, from the Files tab,
> your other text files. Both are Contents writes. Keep it scoped to this one
> repository so the token cannot touch anything else you own.

### 3. Point the dashboard at it

Open **<https://atkoulu.vercel.app>** and go to **Settings → Sync with GitHub**:

| Field | Value |
|---|---|
| Token | The token you just copied |
| Owner | Your GitHub username |
| Repository | The repository you created in step 1 |
| Branch | `main` |
| File path | `data.json` |

**Save settings**, then **Test connection**. You should see your username, your
repository, `private`, and *data file found*. If it says `PUBLIC`, stop and make
the repository private before putting anything real in it.

### 4. Fill it in

- **Settings → Import .ics file** — export your timetable from Peppi,
  Lukkarikone, Google Calendar or Outlook and load it here. Re-importing replaces
  the previous import instead of duplicating it.
- **Courses → Add course** — name, Zoom link, Moodle link. The dashboard suggests
  courses from the calendar events it just imported.
- **Files** — your course folders from this repository, ready to read and edit.

Edits save locally at once and reach GitHub a few seconds later. The pill in the
top right tells you where things stand.

### 5. Your other devices

Repeat step 3 on your phone and laptop with the **same repository** and a token
each (or the same one). Each device pulls on open and pushes after edits.

If two devices edit while one is offline, the dashboard notices — GitHub rejects
a write whose revision is stale — and asks which version to keep instead of
guessing. On a phone, use **Add to Home Screen** so it opens like an app.

---

## Living in this repository

Keep whatever shape you like; the dashboard does not care. A folder per course
is the obvious one:

```
data.json
Statistics/
  exercise 1.ods
  notes.md
Usability/
  essay.odt
```

From the **Files** tab you can browse those folders and edit what is in them.
Saving is a real commit.

- **Text files** (`.md`, `.txt`, `.csv`, `.json`, …) open in a plain editor.
- **Documents** — `.odt`, `.ods`, `.odp`, `.docx`, `.xlsx`, `.pptx` — open one
  line per paragraph. Only paragraphs you change are rewritten; images, styles,
  tables and every other paragraph are repackaged untouched. Formatting *inside*
  a paragraph you edit collapses to its first run, so a bold word mid-sentence
  comes back plain. A raw-XML view sits behind the text view for anything the
  line editor cannot express. In spreadsheets only cell text is editable —
  numbers and formulas are left alone.
- **Everything else** — PDFs, images, and the pre-2007 binary formats (`.doc`,
  `.xls`, `.ppt`, which are not ZIP archives) — is listed with a link to GitHub.
  Text files over 1 MB too; that is a GitHub API limit.

Every save is a commit, so a bad edit is one `git revert` from undone.

The Files tab deliberately cannot create, rename or delete. Do those on GitHub,
or with git on a computer.

---

## Rules worth keeping

**Keep the repository private.** It ends up holding your timetable, your notes,
your deadlines and your coursework.

**Do not commit `.ics` files.** A calendar export names real people, rooms and
groups. `.gitignore` blocks them; import them into the dashboard instead.

**Do not commit tokens.** `.env` files are ignored too. If a token ever leaks,
revoke it at
[github.com/settings/personal-access-tokens](https://github.com/settings/personal-access-tokens)
and make a new one — nothing else is affected.

**Backups.** Settings → *Download backup* writes a JSON file containing
everything, notes and deadlines included. `.gitignore` keeps those out of the
repo on purpose; keep them somewhere else.

---

## FAQ

**Is my data on someone else's server?**
No. `atkoulu.vercel.app` serves static files — HTML, CSS and JavaScript — and
stores nothing. The only network calls the page can make are to `api.github.com`,
enforced by a Content-Security-Policy header. Your data goes from your browser to
your repository and nowhere else.

**Can the owner of that page see my stuff?**
No. Your token lives in your browser's local storage, is scoped to your own
repository, and is never transmitted anywhere but GitHub.

**Can I host the page myself?**
The dashboard's source is a separate, private repository. If you want your own
deployment rather than the hosted page, ask its owner. Using the hosted page with
your own repository and your own token gives you the same isolation either way.

**What if I stop using it?**
Delete the token and keep the repository. `data.json` is plain JSON and your
course folders are ordinary files — nothing is locked in a format only this app
can read.
