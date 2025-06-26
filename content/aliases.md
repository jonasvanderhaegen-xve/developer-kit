---
title: "Shell Aliases Reference"
description: "Detailed breakdown of productivity shell aliases for Laravel Sail, Git, file operations, and system management"
date: 2025-01-26
draft: false
weight: 10
---

# Detailed Breakdown of Your Shell Aliases

Below is an in-depth explanation of every alias defined in your `~/.zshrc`, followed by a line-by-line walkthrough of the `aliases()` (and its shortcut `als`) function.

## 🚀 Laravel Sail Shortcuts

These aliases wrap common Sail commands, so you spend less time typing and more time coding.

| Alias       | Expansion                    | What it does                                                             |
|------------:|------------------------------|---------------------------------------------------------------------------|
| `s`         | `sail `                      | Base command: forwards whatever you type after `s` into `./vendor/bin/sail`.   |
| `sa`        | `sail artisan `              | Runs Artisan commands inside the Sail container.                           |
| `sc`        | `sail composer `             | Runs Composer commands inside the Sail container.                         |
| `sm`        | `sa migrate`                 | Shortcut for `sail artisan migrate`: runs your database migrations.       |
| `smf`       | `sa migrate:fresh`           | Runs `php artisan migrate:fresh` (drops all tables & re-runs migrations).|
| `smfs`      | `sa migrate:fresh --seed`    | Same as `smf` + `--seed`: rebuilds the database and seeds with test data.|
| `sup`       | `s up -d`                    | Starts all Sail services in detached (background) mode.                   |
| `suspend`   | `s stop`                     | Stops all Sail services.                                                  |
| `sus`       | `suspend`                    | A ❝lazy❞ alias for `suspend` (same as `s stop`).                           |
| `sr`        | `sc run dev`                 | Runs `composer run dev` (often starts Webpack/Vite, mix watchers, etc.).  |

---

### How It Works

1. **`s`**  
   - If you type `s up`, it expands to `sail up`.  
   - If you type `s artisan tinker`, it becomes `sail artisan tinker`.

2. **`sa`**  
   - Example: `sa tinker` → `sail artisan tinker`.

3. **`sc`**  
   - Example: `sc require livewire/livewire` → `sail composer require livewire/livewire`.

4. **Migration Helpers (`sm`, `smf`, `smfs`)**  
   - `sm`: Run any pending migrations.  
   - `smf`: Drop & rebuild your schema (useful for a clean slate).  
   - `smfs`: Same clean rebuild plus seed (populates tables with default/test data).

5. **Service Control (`sup`, `suspend`, `sus`)**  
   - `sup`: Start Docker containers in the background (`-d`).  
   - `suspend`/`sus`: Stop all containers.

6. **Dev Runner (`sr`)**  
   - Typically invokes your project's `dev` script defined in `composer.json` (e.g. starts JS/CSS builds, hot reload, etc.).  

---

### Why These Matter

- **Consistency**: Every Sail command lives under `s` → you never forget whether it's `docker-compose` or `sail`.  
- **Speed**: Two-letter shortcuts for the most-used tasks.  
- **Safety**: Encourages use of `migrate:fresh` only when explicitly typed (`smf`), preventing accidental data loss.  

Keep these in your muscle memory and you'll navigate your Laravel Sail environment effortlessly!

---

## 📁 Directory Navigation

| Alias  | Expansion        | What it does                               |
|-------:|------------------|--------------------------------------------|
| `..`   | `cd ..`          | Move up **one** directory level.           |
| `...`  | `cd ../..`       | Move up **two** levels.                    |
| `....` | `cd ../../..`    | Move up **three** levels.                  |
| `back` | `cd ..`          | Same as `..`, but more descriptive.        |
| `back2`| `cd ../..`       | Same as `...`, but more explicit.          |
| `~`    | `cd ~`           | Jump to your **home** directory.           |
| `home` | `cd ~`           | Another way to go home.                    |
| `root` | `cd /`           | Jump to the **root** of the filesystem.    |

> **Why these help:**  
> Rapid "up the tree" movement saves keystrokes when navigating deep project hierarchies. Named aliases (`back`, `back2`) can be easier to remember than dots.

---

## 📋 File Listing

| Alias | Expansion     | Behavior                                          |
|------:|---------------|---------------------------------------------------|
| `l`    | `ls -la`      | Long listing, show **all** files (including `.`). |
| `ll`   | `ls -alF`     | Long listing, classify entries with `/`, `*`, etc.|
| `la`   | `ls -A`       | Show almost all (hides `.` and `..`).             |
| `lm`   | `ls -ltr`     | Sort by **modification** time, oldest first.      |
| `lk`   | `ls -lSr`     | Sort by **size**, smallest first.                 |

> **Why these help:**  
> Frequent `ls` flags become muscle memory. Rather than typing `ls -alF`, you just hit `ll`.

---

## 📁 Directory Operations

| Alias | Expansion      | Purpose                                   |
|------:|----------------|-------------------------------------------|
| `md`  | `mkdir -p`     | Make a directory **and** any parents.     |
| `rd`  | `rmdir`        | Remove an **empty** directory.            |

> **Note:** Replacing `mkdir` with `md` cuts keystrokes, and adding `-p` avoids "parent doesn't exist" errors.

---

## 📝 File Operations (Interactive)

| Alias | Expansion     | Effect                                      |
|------:|---------------|---------------------------------------------|
| `cp`   | `cp -i`       | Prompt before **overwriting** files.        |
| `mv`   | `mv -i`       | Prompt before **moving/renaming**.          |
| `rm`   | `rm -i`       | Prompt before **deleting**.                 |
| `ln`   | `ln -i`       | Prompt before creating a **hard link**.     |

> **Why interactive?**  
> The `-i` flag prevents accidental data loss by asking for confirmation.

---

## ⚡ Git Shortcuts

| Alias | Expansion             | What it does                         |
|------:|-----------------------|--------------------------------------|
| `gs`  | `git status`          | Show current repo status.            |
| `ga`  | `git add`             | Stage **all** changes (by default).  |
| `gc`  | `git commit`          | Commit staged changes.               |
| `gp`  | `git push`            | Push commits to origin.              |
| `gl`  | `git log --oneline`   | Show commit history, one line each.  |

> **Why these help:**  
> Reduces the most-used Git commands to two characters.

---

## 🖥️ System & Network

| Alias    | Expansion           | Purpose                                 |
|---------:|---------------------|-----------------------------------------|
| `c`       | `clear`            | Clears the terminal screen.             |
| `h`       | `history`          | Lists recent commands.                  |
| `j`       | `jobs`             | Shows background jobs.                  |
| `du`      | `du -h`            | Disk usage, **human-readable**.         |
| `df`      | `df -h`            | Filesystem free space, **human-readable**. |
| `free`    | `free -h`          | Memory usage, **human-readable**.       |
| `ip`      | `curl ifconfig.me` | Query your external IP address.         |
| `ports`   | `netstat -tuln`    | List listening TCP/UDP ports.           |

> **Why these help:**  
> One-letter shortcuts for everyday checks keep you in flow.

---

## 🔄 Config Editing & Reload

| Alias   | Expansion                    | Action                                        |
|--------:|------------------------------|-----------------------------------------------|
| `zshrc`  | `nano ~/.zshrc`             | Open your Zsh config in Nano.                |
| `bashrc` | `nano ~/.bashrc`            | Open Bash config.                             |
| `vimrc`  | `nano ~/.vimrc`             | Open Vim config.                              |
| `reload` | `source ~/.zshrc`           | Apply changes to your current shell session.  |
| `src`    | `source ~/.zshrc`           | Same as `reload`.                             |

> **Why these help:**  
> Quick access and live reload without restarting the terminal.

---

## 🔍 Alias Browser: `aliases()` & `als`

### Function Code

```zsh
function aliases() {
    ...
}
alias als='aliases'
```

### How It Works

1. **No Arguments (`aliases`)**

   * Prints section headers (e.g. "📁 Directory Navigation:").
   * Uses `alias | grep -E <pattern>` to filter only the relevant aliases for each category.
   * `sort` ensures they're in alphabetical order.

2. **With Arguments (`aliases foo`)**

   * Treats all given words as one search string (`"$*"`).
   * First, runs `grep -i` to find any alias whose name or expansion contains that term (case-insensitive).
   * Then runs another `grep -E` to catch aliases where your search term appears at the start of the alias name or as part of the `name=` pattern, giving "related" hits.

3. **Shorthand `als`**

   * Simply points to `aliases`, so you can type `als` instead of spelling out `aliases`.

> **Example Usage:**
>
> * `aliases` → See every alias grouped by purpose.
> * `als git` → Show only Git-related shortcuts (`gs`, `ga`, `gc`, etc.).
> * `als ..` → Find your "go up" directory aliases.

---

### Why It's Useful

* **Organization:** Groups 80+ aliases into logical sections.
* **Discoverability:** You rarely remember every shortcut; `als` lets you search on the fly.
* **Maintainability:** New aliases you add will automatically show up in the right section (as long as they match the grep pattern).

---

With this overview, you now know exactly what each shortcut and function does—maximizing your productivity in WSL!