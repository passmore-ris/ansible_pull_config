# ansible_pull_config
This contains the ansible configuration used to configure Linux workstations running ansible-pull. This playbook is only confirmed to work on **Debian 13** at the moment. It is deigned to set up a school workstation running Debian, such that pupil and staff accounts in a FreeIPA server can be used on any workstation with roaming home directories.

## Assumptions
- Ideally, each workstation has been joined to a FreeIPA domain with roaming home directories configured using NFS. I have been able to automate this through Ansible [here](https://github.com/passmore-ris/freeipa-ansible).
- An instance of NetBird has been set up for the organisation. When configured properly, this will allow company laptops to still access the IPA and NFS server remotely, so they may be taken home.
- A workstation has a local Ansible fact in the file `/etc/ansible/facts.d/workstation.fact` to denote whether the workstation is meant for staff or pupils. The fact file takes the form:
```
[role]
name = staff
```


## Variables
There are variables that should be set prior to running ansible-pull:
- 'veyon_download_path': This simply identifies where the Veyon .deb package should be stored. It can be left alone safely.
- 'netbird_management_url': This states the management URL of the NetBird instance which has been set up.
### Secret Variables
These variables should be encrypted by ansible-vault. Please note that the vault password must be distributed to each workstation for this to run. This can, of course, be automated with Ansible.
- *netbird_setup_key_pupil*: This is a setup key which allows the workstation to immediately authenticate and connect to the NetBird instance in the "Pupils" group.
- *netbird_setup_key_staff*: This is a setup key which allows the workstation to immediately authenticate and connect to the NetBird instance in the "Staff" group.
- *rustdesk_password*: The RustDesk password which can be used to connect to workstations for remote access.
The names of the NetBird and RustDesk variables can be changed, but note that these will have to be altered in the *netbird.yml* and *rustdesk.yml* tasks as well.

## Running the playbook
On the target machine, run the command:
```
sudo ansible-pull -U https://github.com/passmore-ris/ansible_pull_config.git -d /opt/ansible --vault-password /opt/.vault-password
```
This can be applied to multiple workstations simultaneously by executing it as an ad-hoc Ansible command.

## Tasks
Here are the tasks to be undertaken by the playbook. These can be seen in the file *local.yml*.

### Change Repositories
The repositories are being changed to Yandex mirrors, since the organisation is based in Russia.

### Apply Bugfixes
Various bugs that have been found are fixed here. These include:
- disabling suspend and hibernate on laptops, as this wreaks havoc with NFS and causes the laptop to freeze for a considerable length of time.
- fixing "phantom brightness" whereby the brightness would randomly jump to the maximum or minimum value every now and again.

### Install Packages
Since different packages are to be installed for staff and pupils, the packages installations are split into three sections:
1. Packages for both staff and pupil workstations
2. Packages for pupil workstations only
3. Packages for staff workstations only
Installing via *apt* is the preferred method, however installation via flatpaks has been used when this option was not available.
The packages installed can be seen below.
#### Packages for both staff and pupil workstations:
- Sublime
- VLC
- OnlyOffice
- Obsidian
#### Packages for pupil workstations only:
- Blender
- Inkscape
- Geany (and plugins)
- Thonny
- GIMP
- Audacity
- Dia
- Sqlitebrowser
- tmux
- libreoffice-l10n-ru # Russian language pack for LibreOffice
- VS Code
- Tuxpaint
- Turbowarp
#### Packages for staff workstations only:
- Google Chrome
- Errands
- Okular
- Rustdesk
RustDesk's installation has its own task set which also sets the RustDesk password.

### Edit Thonny
Thonny needs to be edited a bit before it can be deployed. This includes removing a certain flag from its interface.

### Add Wallpapers
Some wallpapers have been added for aesthetic purposes. This also includes the school logo in the icons directory for Debian XFCE.

### Set up cron job
The playbook also creates a sudo user called **ansible**, and gives it a cron job of running ansible-pull on the repository every ten minutes, and only executing ansible-pull if there have been any changes to this repository.

### Make Users

#### Enable Auto-creation of Home Directories
On Debian-based systems, auto-creation of home creation needs to be enabled. This is so that domain users that log in have a home directory to land in. On RedHat-based systems, this feature is enabled by deafult.

#### Enable Manual Login
Users must type in their username manually, so that any domain user can log in to any workstation.

#### Mount User Home Directory
Mount the user's home directory located in a separate file server onto their home profile.

### Set Up Veyon
Installs and configures Veyon on the host machine. Members of the domain group **Pupils** are prevented from executing */usr/bin/veyon-master*.

### Configure /etc/skel
This configures the new home profile created for each user. So far, the main feature is a Mozilla Firefox profile.
