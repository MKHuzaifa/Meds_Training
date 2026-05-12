# Module 1 — Day 1: Linux CLI exercises (solutions)

Work from your course folder if you like, for example:

```bash
cd "/path/to/MEDS/Module 1/day 1"
```

All commands below assume a typical Linux shell (bash) or **Git Bash / WSL** on Windows. On native Windows PowerShell, use the equivalents noted where it matters.

---

## Exercise 1 — Directory tree with `mkdir` and `touch`

**Goal:** Create  
`project/{src/{rtl,tb,include},docs,scripts,build}`  
then verify with `tree`.

### Option A — One-shot with brace expansion (bash)

```bash
mkdir -p project/src/{rtl,tb,include} project/docs project/scripts project/build
touch project/src/rtl/.gitkeep project/src/tb/.gitkeep project/src/include/.gitkeep \
      project/docs/.gitkeep project/scripts/.gitkeep project/build/.gitkeep
```

(You can omit the `touch` lines if empty directories are enough; some systems do not track empty dirs in git without a placeholder file.)

### Option B — Only `mkdir` and `touch` (no `-p` brace shortcut), step by step

```bash
mkdir project
mkdir project/src project/docs project/scripts project/build
mkdir project/src/rtl project/src/tb project/src/include
touch project/src/rtl/.gitkeep
touch project/src/tb/.gitkeep
touch project/src/include/.gitkeep
touch project/docs/.gitkeep
touch project/scripts/.gitkeep
touch project/build/.gitkeep
```

### Verify

```bash
tree project
# or, if tree is not installed:
find project -type d | sort
```

---

## Exercise 2 — Download RISC-V ISA manual; inspect with `ls` and `file`

**Goal:** Download the PDF, then report human-readable size, permissions, and file type.

### Download (pick one)

```bash
mkdir -p downloads
curl -L -o downloads/riscv-unpriv-isa.pdf \
  "https://github.com/riscv/riscv-isa-manual/releases/download/20240411/unpriv-isa-asciidoc.pdf"
```

```bash
# Alternative:
wget -O downloads/riscv-unpriv-isa.pdf \
  "https://github.com/riscv/riscv-isa-manual/releases/download/20240411/unpriv-isa-asciidoc.pdf"
```

### Size and permissions

```bash
ls -lh downloads/riscv-unpriv-isa.pdf
```

### File type (magic bytes / format)

```bash
file downloads/riscv-unpriv-isa.pdf
```

You should see something like **PDF document**, version …, plus page count metadata depending on your `file` version.

---

## Exercise 3 — 100 random lines → sort → unique counts → top 10 to a file

**Goal:** Generate 100 lines, sort, count occurrences of each unique line, keep the 10 most frequent lines in a new file.

### Generate 100 random lines (example: random bytes as hex)

```bash
openssl rand -hex 16 | …   # not 100 lines by itself
```

Better one-liner for **100 lines** of random text:

```bash
for i in $(seq 1 100); do openssl rand -hex 8; done > random_lines.txt
```

(If you want **duplicate lines** so “top 10” is interesting, draw from a small word list instead of fully random hex.)

### Sort, unique, count, top 10 → new file

```bash
sort random_lines.txt | uniq -c | sort -nr | head -n 10 > top10_frequent.txt
```

Explanation:

| Stage        | Command        | Role                                      |
|-------------|----------------|-------------------------------------------|
| Sort        | `sort`         | Groups identical lines together         |
| Unique+count| `uniq -c`      | One line per unique string + count        |
| Sort by count | `sort -nr`   | Numeric sort, descending (highest first)  |
| Top 10      | `head -n 10`   | First 10 rows → `top10_frequent.txt`      |

---

## Exercise 4 — SSH keys and GitHub (`ssh -T`)

**Goal:** Key-based auth to GitHub; success message from `ssh -T git@github.com`; screenshot for your submission.

### 1) Create a key (ed25519; skip if you already have one for GitHub)

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519_github
```

Press Enter for empty passphrase (or set one for stronger security).

### 2) Start agent and add key (Linux / macOS typical)

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_github
```

### 3) Copy public key and add it in GitHub

**GitHub → Settings → SSH and GPG keys → New SSH key** — paste the contents of:

```bash
cat ~/.ssh/id_ed25519_github.pub
```

If you used the default path `~/.ssh/id_ed25519`, use that `.pub` file instead.

### 4) Test

```bash
ssh -T git@github.com
```

Expected success (wording may vary slightly):

```text
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

Take a **screenshot** of that terminal output for the exercise.

**Windows note:** Same flow in **Git Bash** or **WSL**; ensure `ssh-agent` and `ssh-add` match your environment (PowerShell can use OpenSSH built into Windows with `Start-Service ssh-agent` if needed).

---

## Exercise 6 (Bonus) — Largest `.c` files under `/usr/include`

**Goal:** One pipeline: find all `.c` files, count lines in each, sort by line count descending, show top 5.

When `wc -l` receives many filenames, it prints one line per file **and** a final summary line ending in `total`. That summary line’s count is the sum of all files, so it would often sort to the top and break “top 5 files.” Drop it, then sort:

```bash
find /usr/include -type f -name '*.c' -exec wc -l {} + | awk '!/total$/' | sort -nr | head -n 5
```

`find … -exec wc -l {} +` batches files so `wc` runs fewer times than one invocation per path.

**Alternative** (same idea, `grep` instead of `awk`):

```bash
find /usr/include -type f -name '*.c' -print0 | xargs -0 wc -l | grep -Ev ' total$' | sort -nr | head -n 5
```

**macOS / BSD note:** If `find` does not support `{} +`, use the `xargs -0` form above.

**WSL/Linux only:** On Windows there is no `/usr/include`; run this inside **WSL** or a Linux VM.

---

## Quick checklist

| Exercise | Main tools                          |
|----------|-------------------------------------|
| 1        | `mkdir`, `touch`, `tree` / `find`   |
| 2        | `curl` or `wget`, `ls -lh`, `file`  |
| 3        | `sort`, `uniq -c`, `sort -nr`, `head` |
| 4        | `ssh-keygen`, `ssh-add`, `ssh -T`   |
| 6 (bonus)| `find`, `wc -l`, `sort`, `head`     |

You can copy this file into `D:\MEDS\Module 1\day 1\` on your machine and rename it if your course expects it there.
