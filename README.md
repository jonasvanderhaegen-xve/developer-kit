> [!IMPORTANT]
> **Note:** Do not follow these instructions yet until this notice is removed.<br>
> **Planned:**
> - Feature: [Nginx Proxy Manager](https://nginxproxymanager.com/) to maintain your custom local domains -> done
> - Feature: Alternative to Laravel Herd Expose? ngrok perhaps Does laravel sail have a similar service available?
> - Bug: Redirect to apps/ only if shell goes to System32 -> done

# XVE Dev Setup on Windows 11

## ⚡ Total Estimated Time: 14–29 minutes from step 1 to 7


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

[Read more about Detailed Breakdown of Your Shell Aliases](aliases.md)

