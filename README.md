# Git-SSH-Windows
Some documentation on how to use SSH with Git on Windows using OpenSSH.

### Motivation
Most of the documentation on using SSH with Git on Windows leave important stuff out or just explain a few things in detail and forget about edge cases like configuring Git properly. That's why this repo should act as a start to finish guide.

## Prerequisites
1. Install OpenSSH Client & Server (https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui#install-openssh-for-windows)
2. Install Git (https://git-scm.com/download/win)
3. Configure Git to use external OpenSSH library (from Windows)

## Start the ssh-agent
### Via command line:
Execute this script via Powershell:
```ps
# Set the sshd service to be started automatically
Get-Service -Name ssh-agent | Set-Service -StartupType Automatic

# Now start the ssh-agent service
Start-Service ssh-agent
```
### Via GUI (manual way)
1. Open the services app: \
Press ``Windows`` + ``R`` and type ``services.msc`` into the box and hit enter
2. Find ``OpenSSH Authentication Agent`` in the list
3. Right click on it and select ``Properties``
4. Set ``Startup Type`` to ``Automatic``
5. Start the service by clicng on ``Start``
6. Save & close the page by clicking ``OK``

## Verify that the agent is running
Check if the service is up and running by typing the following into the terminal:
```batch
ssh-agent
```
* If you do not get any errors and just a blank line everything works!
* If you receive the error ``unable to start ssh-agent service, error :1058`` or something similiar the agent is not running

## Generate an SSH key
1. Type ``ssh-keygen`` into your terminal
2. Follow the steps and hit enter (you don't need to set a passphrase)
3. After that you should get a similiar output:
```
C:\Users\WindowsUser>ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\WindowsUser/.ssh/id_rsa): abc
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\WindowsUser/.ssh/id_rsa
Your public key has been saved in C:\Users\WindowsUser/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:Q9ABmisicMu5obwH8rpDYbJARklmNC4/aGvURcm6iVA WindowsUser@YourHostname
The key's randomart image is:
+---[RSA 3072]----+
|            oo.*+|
|           +.==.+|
|        o ..X .o.|
|       . *o+E= . |
|      . S Oo+    |
|     ..+ + B .   |
|    . oo  ..=    |
|     .  +=+. .   |
|        o=o..    |
+----[SHA256]-----+
```
4. If you specified a file name or path for the key you need to manually add it to the agent

## Adding the SSH key to the agent
This step is only needed if your key isn't saved in ``.ssh/id_rsa``
1. Open a new terminal
2. Navigate to your ssh key pair directory \
Default: ``cd %USERPROFILE%\.ssh``
3. Add the (private) key to the agent using this command: ``ssh-add yourKeyName`` \
Example: if you called your ``max@surface`` you would type ``ssh-add max@surface``
4. If everything worked you should see something similiar to this:
```
C:\Users\WindowsUser>ssh-add max@surface
Identity added: max@surface (WindowsUser@YourHostname)
```
5. Done!

## Use your key on GitHub, GitLab, BitBucket etc.
The process is the same on any Git server. This exmaple shows you on how to do it on GitHub:
1. Go to https://github.com/settings/ssh/new
2. Set the key type to ``Authentication Key``
3. Open your public key file (e.g. ``max@surface.pub``)
4. Copy the content of the file into the key box
5. Set a title, best would be the same as the key's name (e.g. ``max@surface.pub``)
6. Click on ``Add SSH key``. Done!

## Authenticate with your key
Start by cloning a private repository.
1. Get the SSH Uri of your repository
2. Open a new terminal in the parent folder of where you want to clone your repo
3. Type ``git clone`` with the link you copied earlier: ``git clone SshUriOfYourRepo`` \
Example: ``git clone git@github.com:TheDusty01/SomeSecretRepo.git``
4. If you get following message ``Are you sure you want to continue connecting (yes/no/[fingerprint])?`` type ``yes`` and hit enter
5. Once it's done you should get a similiar output:
```
C:\Development\Repos>git clone git@github.com:TheDusty01/SomeSecretRepo.git
Cloning into 'SomeSecretRepo'...
The authenticity of host 'github.com (140.82.121.4)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])?
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), 4.54 KiB | 4.54 MiB/s, done.
```
6. Done! You successfully cloned your repo over SSH. \
If you got any errors or were prompted to enter a password you haven't properly added your SSH key to the agent.

## XY is not properly explained
If you think that something is not explained enough in detail feel free to open a PR and edit the coresponding section :)


