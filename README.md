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
yet and must be sent to one of the `scrapers` using `agentctl`.

Generate the configs to upload to the scraping service API:

```
ansible-playbook -i ./scrape_targets/inventory scrape_targets/generate.yaml
```

Then, examine the contents of `machine.txt` at the root of this repository.
Copy the IP address for `scraping_service_a` and run this command:

```
export SCRAPING_SERVICE_ADDR=<paste here>
```

When running that command, replace `<paste here>` with the address that was
copied.

Finally, run the following command to sync the config files:

```
agentctl config-sync -a http://${SCRAPING_SERVICE_ADDR}:12345 ./scrape_targets/configs
```
