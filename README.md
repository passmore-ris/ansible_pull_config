# ansible_pull_config
This contains the ansible configuration used to configure Linux workstations running ansible pull.

## Actions
Here are the actions performed by the playbook:
### Repositories
Changes the default repositories to Yandex mirrors, since the workstations are based in Russia. So far this is configured for the following types:
- Linux Mint

### Install Packages
The following packages are set to be installed:
- htop
- Openshot
- Anki

### Cron job
The playbook also creates a sudo user called **ansible**, and gives it a cron job of running ansible-pull on the repository every ten minutes.
