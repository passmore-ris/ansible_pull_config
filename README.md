# ansible_pull_config
This contains the ansible configuration used to configure Linux workstations running ansible pull. This playbook is only confirmed to work on **Linux Mint** at the moment. It is deigned to set up a school desktop running Linux Mint, such that pupils' accounts in an Active Directory server can be used on any workstation, with a roaming "MySharedFolders" folder.

## Requirements
For this to be run, the following is needed:
- Linux Mint workstations.
- An Active Directory server
- A file server with home directories for each user.
The variables in *local.yml* should be changed prior to its use.

## Running the playbook
On the target machine, run the command
```
sudo ansible-pull -U https://github.com/passmore-ris/ansible_pull_config.git --extra-vars "domain_admin_username=smith.j domain_admin_password=supersecretpassword"
```
replacing the extra variables with the required values.

## Tasks
Here are the tasks to be undertaken by the playbook. These can be seen in the file *local.yml*.

### Install snap
Ensure *snap* is installed to allow for installation of certain packages.

### Install Packages
The following packages are set to be installed on the workstations:
- htop
- Openshot
- Anki
- Inkscape
- Geany
- Thonny
- GIMP
- Audacity
- Dia
- Turbowarp
- Blender
- Pinta
- SqliteBrowser
- Sublime
- tmux

### Edit Thonny
Thonny needs to be edited a bit before it can be deployed.

### Set up cron job
The playbook also creates a sudo user called **ansible**, and gives it a cron job of running ansible-pull on the repository every ten minutes, and only executing ansible-pull if there have been any changes to this repository.

### Join Domain
Joins the domain specified in the variables in *local.yml*.
The variable "domain_admin_password" and "domain_admin_password" must be specified in the '--extra-vars' argument in the original ansible-pull command.

#### Enable Auto-creation of Home Directories
On Debian-based systems, auto-creation of home creation needs to be enabled. This is so that domain users that log in have a home directory to land in. On RedHat-based systems, this feature is enabled by deafult.

#### Enable Manual Login
Users must type in their username manually, so that any domain user can log in to any workstation.

#### Mount User Home Directory
Mount the user's home directory located in a separate file server onto their home profile.

### Set Up Veyon
Installs and configures Veyon on the host machine. Members of the domain group **Pupils** are prevented from executing */usr/bin/veyon-master*.

### Configure /etc/skel
This configures the new home profile created for each user. So far, the main feature is a Mozilla Firefox profile. The default language of LibreOffice has also been changed to Russian.
