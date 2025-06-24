# For XVE dev team with Windows 11

0. Run in terminal

```powershell
    wsl --install
```

1. [Install Docker desktop](https://www.docker.com/)

2. Add your ssh key of your laptop device to your work github profile.

3. Download [install.ps1](https://raw.githubusercontent.com/jonasvanderhaegen-xve/windows-powershell-wsl-distro-importer/refs/heads/main/install.ps1) on your windows pc, probably in downloads folder.

4. Open terminal (administrator), and navigate towards Downloads folder, run script.

```powershell
    cd Downloads
    .\install.ps1
```

5. This will download from [jonasvanderhaegen-xve/xve-artifacts](https://github.comjonasvanderhaegen-xve/xve-artifacts/releases) and mount a WSL distribution on your windows pc.

6. After succesful installation, open Docker Desktop<br>
**Settings → Resources → WSL Integration → Toggle XVE → Click Apply and Restart**

7. Close all explorer windows and terminal windows, otherwise it will not appear in the already opened ones.

8. To test, you can enter WSL through many ways, right click on terminal, in terminal of your IDE code editor, in terminal right of the tabs with the dropdown.

9. Once entered it will search for your ssh keys and symlink to your users home folder in WSL. \\WSL$\xve\home\.ssh

10. Git clone a private repository in /apps folder

```bash
  git clone git@github.com:XVE-BV/test-wsl-git-repo.git
  cd test-wsl-git-repo
  ./scripts/sail.sh
```

12. This will set your environment variables correctly, run composer install within the docker environment to get you laravel/sail composer package to then build the actual docker container, run migrations, offers to seed, installs npm dependencies.

13. Run following command to start your laravel instance

```bash
  sr  # sail composer run dev,
      # which runs concurrently php artisan serve, runs your queue worker,
      # and Laravel Pail, allows you to easily dive into your log files directly from the command line
```

## WSL? Whut?

It's a alpine linux distribution tailored to our minimum viable needs. In my opinion it should be kept at a minimum viable product so not much more maintenance or patches need to be distributed to all devs.<br> 
It's only purpose is to provide you a nice local environment to work in without all the headache and struggles.<br>
No PHP, no composer, no node or npm. All of this should be provided by inside each project's docker setup.

The Laravel sail images are maintained by Laravel. I'm not going to write patches to keep everyone's WSL php/composer/node/npm versions up-to-date. The WSL is meant to live long on your desktop. <br>
The projects and docker containers.. not so much. In an ideal world we just run migrations and seeders and our project is ready to go and easily be destroyed without hassle to re-build.

Upon login it will symlink your ssh keys and also run a Nginx Proxy manager for maintaining local domains with secure SSL (https). (WIP)
