# 📚 Reading Journal

A running log of every book I read, grouped by year — title, author, and the subtitle or one-line takeaway that says what the book is actually about.

This repo is deliberately small. There is no build step, no site generator, and no tooling to install. It is a single Markdown file that grows one line at a time, with git history acting as the timestamp for when each book was finished.

---

## What's in here

| File | What it is |
|---|---|
| `Reading-Journal.md` | The journal itself — the running list of books, newest year first. This is the file you actually read and edit. |
| `README.md` | This guide. |

That's the whole repo. If you're just here to browse, open [`Reading-Journal.md`](Reading-Journal.md) and stop reading this file.

---

## How the journal is laid out

The file opens with a short header, then a per-year section. Each year has a running total followed by a numbered list of books:

```markdown
# 📚 Vincent's Reading Journal

A reading adventure, books & discoveries

**Total books read in 2026:** 7

**2026:**
<br>
1. 📚 <b>Learning How to Learn</b> by *Barbara Oakley* ~ How to Succeed in School Without Spending All Your Time Studying.
2. 🌱 <b>Garden Hacks</b> by *Filip Johansson* ~ 70 Smart, Sustainable Tips for Gardeners.
```

### The entry format

Every line follows the same shape:

```
<number>. <emoji> <b>Title</b> by *Author* ~ Subtitle or takeaway
```

Four parts, in order:

- **Number** — position in that year's reading, in the order finished.
- **Emoji** — a quick visual tag for the subject. Not a fixed taxonomy; pick whatever fits (📚 learning, 🌱 gardening, ⚡️ energy/health, 🚴‍♂️ cycling, 🌎 society, ☺️ wellbeing).
- **`<b>Title</b>`** — the book title, bolded with inline HTML so it renders the same on GitHub and in most Markdown previewers.
- **`*Author*`** — the author in italics. Multiple authors go on one line, separated by `&` or commas.
- **`~ Subtitle`** — everything after the tilde is the book's subtitle, or your own one-line summary if the subtitle isn't useful.

The mix of Markdown (`*italics*`) and HTML (`<b>`) is intentional and already established across the file — match it rather than converting entries to pure Markdown, so the list stays visually consistent.

### Years

Each year is its own block, introduced by `**<year>:**` followed by `<br>`. Numbering restarts at 1 for each year, and each year carries its own `**Total books read in <year>:**` line directly above it.

---

## Adding a book

1. Open `Reading-Journal.md`.
2. Append a new numbered line to the bottom of the current year's list, following the format above.
3. Bump the `**Total books read in <year>:**` count for that year.
4. Commit with a message that names what changed:

```bash
git add Reading-Journal.md
git commit -m "Add book entry for 'Title'"
git push
```

The existing history uses short, descriptive commit messages of exactly this kind — `Add book entry for 'Ace The Data Science Interview'`, `Update total books read in 2026 to 4`. Keeping that style means `git log --oneline` reads as a chronological record of when each book was finished, which is the closest thing this repo has to a date field.

### Starting a new year

At the turn of the year, add a new block **above** the previous year's, so the most recent year stays at the top of the file:

```markdown
**Total books read in 2027:** 1

**2027:**
<br>
1. 📖 <b>First Book of the Year</b> by *Author* ~ Its subtitle.
```

Leave the previous year's block untouched below it — past years are a permanent record, not something to edit.

---

## Browsing and searching

The journal is plain text tracked in git, which means two different kinds of question have
easy answers: *what* is in the list (ordinary text search) and *when* it got there (git
history). No tooling required — everything below is a one-liner from the repo root.

**Find a book by author, title, or topic.** `grep` prints every line containing the text
you give it; `-i` makes the match case-insensitive, so `oakley` finds `Oakley`:

```bash
grep -i "oakley" Reading-Journal.md
```

**Check a year's total against the actual list.** The `**Total books read**` line is
maintained by hand, so it can drift out of step with the entries below it. This counts the
entries for real. The `sed` half slices out just the 2026 block — from the `**2026:**`
header down to the next line beginning `**20`, i.e. the following year — and the `grep -c`
half counts the lines in that slice starting with a digit, which is one per book:

```bash
sed -n '/\*\*2026:\*\*/,/^\*\*20/p' Reading-Journal.md | grep -c '^[0-9]'
```

**See when each book was finished.** The journal records *what* you read but never *when*.
Because each book is added in its own commit, the git history is that missing date column.
`--follow` keeps the trail intact if the file is ever renamed:

```bash
git log --follow --date=short --format='%h %ad %s' Reading-Journal.md
```

```
5db7374 2026-08-31 Update Reading-Journal.md
f969644 2026-06-04 Add book entry for 'Ace The Data Science Interview'
41cddbc 2026-03-12 Add header and initial entry to Reading Journal
```

**Read the journal as it stood on some past date.** `git rev-list -1 --before=<date> HEAD`
finds the last commit made before that date and prints its hash; `git show <hash>:<file>`
then prints the file as of that commit:

```bash
git show "$(git rev-list -1 --before='2026-06-01' HEAD):Reading-Journal.md"
```

The file only ever shows where the list stands today. The history is what shows the pace —
how long a book took, which months were busy, where a year stalled.

---

## Conventions worth keeping

- **Append, don't reorder.** Entries are in finishing order. Renumbering to sort alphabetically would destroy that signal.
- **Keep the total in sync.** The count line and the list are maintained by hand, so they can drift apart. Update both in the same commit.
- **One commit per book.** It keeps the history usable as a timeline.
- **Don't delete past years.** The point of the journal is accumulation.
