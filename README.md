# XVE Dev Setup on Windows 11


## What’s XVE’s WSL Distro?

* A minimal Alpine Linux image tailored for our Docker-based workflows.
* **No** PHP, Composer, Node or npm installed in WSL itself—those live in each project’s containers.
* Keeps WSL lean and low-maintenance; you rebuild project containers as needed without updating your base distro.

> **Note:** On login, WSL also symlinks your SSH keys and (WIP) runs an Nginx Proxy Manager for local HTTPS domains.


1. **Enable WSL**  
   Open PowerShell as Administrator and run:

   ```powershell
      wsl --install
   ````

2. **Install Docker Desktop**
   Download and install from the [Docker website](https://www.docker.com/).

3. **Add your SSH key**
   Upload your laptop’s public key to your work GitHub profile.

4. **Download the installer script**
 
   <a href="https://raw.githubusercontent.com/jonasvanderhaegen-xve/windows-powershell-wsl-distro-importer/main/install.ps1" download>Download install.ps1</a> (e.g. to `Downloads`).


6. **Run the installer**
   In an elevated PowerShell session:

   ```powershell
   cd ~/Downloads
   .\install.ps1
   ```

   This pulls the XVE artifacts and registers a custom Alpine WSL distro.

7. **Enable WSL integration in Docker**

   1. Open **Docker Desktop**
   2. Go to **Settings → Resources → WSL Integration**
   3. Toggle **XVE** on
   4. Click **Apply & Restart**

8. **Restart your terminals**
   Close all File Explorer and terminal windows so they pick up the new WSL distro.

9. **Enter WSL**
   You can open a WSL shell by:

   * Right-clicking in your IDE’s terminal
   * Selecting the dropdown in Windows Terminal
   * Running `wsl` from any PowerShell/CMD prompt

10. **SSH key symlink**
   On first login, your Windows SSH keys will be linked into WSL at:

   ```bash
      \\wsl$\xve\home\<your-user>\.ssh
   ```

11. **Clone & launch a test repo**

    ```bash
       cd /apps
       git clone git@github.com:XVE-BV/test-wsl-git-repo.git
       cd test-wsl-git-repo
       ./scripts/sail.sh
    ```

    This will set environment variables, run `composer install` inside Docker, build containers, run migrations (and seeders), and install npm packages.

12. **Start Laravel Sail**

    ```bash
       sr
    ```

    The `sr` alias runs Sail’s dev tasks: `php artisan serve`, your queue worker, and a log-viewer shell.

---

# Detailed Breakdown of Your Shell Aliases

Below is an in-depth explanation of every alias defined in your `~/.zshrc`, followed by a line-by-line walkthrough of the `aliases()` (and its shortcut `als`) function.

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
> Rapid “up the tree” movement saves keystrokes when navigating deep project hierarchies. Named aliases (`back`, `back2`) can be easier to remember than dots.

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

> **Note:** Replacing `mkdir` with `md` cuts keystrokes, and adding `-p` avoids “parent doesn’t exist” errors.

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
````

### How It Works

1. **No Arguments (`aliases`)**

   * Prints section headers (e.g. “📁 Directory Navigation:”).
   * Uses `alias | grep -E <pattern>` to filter only the relevant aliases for each category.
   * `sort` ensures they’re in alphabetical order.

2. **With Arguments (`aliases foo`)**

   * Treats all given words as one search string (`"$*"`).
   * First, runs `grep -i` to find any alias whose name or expansion contains that term (case-insensitive).
   * Then runs another `grep -E` to catch aliases where your search term appears at the start of the alias name or as part of the `name=` pattern, giving “related” hits.

3. **Shorthand `als`**

   * Simply points to `aliases`, so you can type `als` instead of spelling out `aliases`.

> **Example Usage:**
>
> * `aliases` → See every alias grouped by purpose.
> * `als git` → Show only Git-related shortcuts (`gs`, `ga`, `gc`, etc.).
> * `als ..` → Find your “go up” directory aliases.

---

### Why It’s Useful

* **Organization:** Groups 80+ aliases into logical sections.
* **Discoverability:** You rarely remember every shortcut; `als` lets you search on the fly.
* **Maintainability:** New aliases you add will automatically show up in the right section (as long as they match the grep pattern).

---

With this overview, you now know exactly what each shortcut and function does—maximizing your productivity in WSL!

