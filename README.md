# agent-demo

WIP, not functional yet

## Dependencies

- Agentctl
- Ansible
- Vagrant >= 2.2.10
- VirtualBox >= 6

## Running

Note: This has only been tested on macOS. It should work for Windows and Linux
but WSL 2 users might have some trouble.

1. Copy `provisioning/secrets.yaml.example` to `provisioning/secrets.yaml`.
2. Fill in the values for `remote_write_url`, `remote_write_username` and
   `remote_write_password` to point to your Grafana Cloud Hosted Prometheus
   instance.
3. Launch the Virtual Machines: `vagrant up`
  1. Allow this command a few minutes to complete. If it did not detect your
     network bridge, `vagrant` will prompt you for picking a network bridge
     for each of your five Virtual Machines. If this happens, pick the network
     you want to expose your VMs on, allowing you to access them from your host
     OS.
4. Provision the Virtual Machines: `ansible-playbook -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory provisioning/playbook.yml`

After the machines are provisioned, they will start sending data to Grafana
Cloud. However, only the Agent and Machine metrics are being collected and sent.
The `team` [application](https://github.com/grafana/tns) is not being scraped
yet and must be sent to one of the `scrapers` using `agentctl`:

```
# TODO(rfratto): command
```
