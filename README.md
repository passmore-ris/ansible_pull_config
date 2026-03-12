# ansible_pull_config
This contains the ansible configuration used to configure Linux workstations running ansible pull.

## Actions
Here are the actions performed by the playbook:

### Install Packages
The following packages are set to be installed:
- htop
- Openshot
- Anki

### Join Domain
The variable "domain_admin_password" and "domain_admin_password" must be specified in the '--extra-vars' argument in the original ansible-pull command.
```
sudo ansible-pull -U https://github.com/passmore-ris/ansible_pull_config.git --extra-vars "domain_admin_username=smith.j domain_admin_password=supersecretpassword"

### Cron job
The playbook also creates a sudo user called **ansible**, and gives it a cron job of running ansible-pull on the repository every ten minutes.
