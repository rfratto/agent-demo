# -*- mode: ruby -*-
# vi: set ft=ruby :

# Define the list of machines we want to launch VMs for. The name field represents
# the identified name in Vagrant. The underscore will be replaced by a hyphen to make
# up the machine's hostname. The IP will define its internal IP address, and the role
# defines the ansible group the node will be assigned to.
machines = [
  # The "team" machines will be responsible for running "team software,"
  # which (for the sake of the demo) is going to be a piece of software
  # that a team wants to monitor.
  #
  # The "team" machines will still run the Agent for collecting system
  # metrics but the software they run will be scraped by the scraping
  # server machines.
  { name: "team_a", ip: "10.0.10.2", role: "team" },
  { name: "team_b", ip: "10.0.10.3", role: "team" },

  # The "etcd" machine just runs ETCD. This machine is used by the
  # scraping server machines for registering the cluster and for the nodes
  # to discover one another.
  { name: "etcd_a", ip: "10.0.11.2", role: "etcd" },

  # The "scraping server" machines will be responsible for running a
  # scraping service cluster to manage a significant number of scrape
  # load.
  #
  # Like the "team" machines, the "scraping server" machines will also
  # run an Agent (aside from the clustered agent) for the purposes of
  # scraping its own system metrics.
  #
  # The first scraping_server will be responsible for running ETCD.
  { name: "scraping_server_a", ip: "10.0.12.2", role: "scraper" },
  { name: "scraping_server_b", ip: "10.0.12.3", role: "scraper" },
  { name: "scraping_server_c", ip: "10.0.12.4", role: "scraper" },
]

# define_machine creates a new machine to use for the demo. Every machine will
# have an ip which determines their **internal** IP address used for internal
# communication.
#
# The created machine will also be attached to the public network to make it
# easier to access services on it from the host machine; an alternative could
# be to selectively forward ports here but doing it this way reduces the
# complexity of the Vagrantfile.
def define_machine(config, name, ip)
  config.vm.define name do |machine|
    machine.vm.box = "hashicorp/bionic64"
    machine.vm.hostname = name.gsub('_', '-')

    # eth1: private_network, eth2: public_network
    machine.vm.network "private_network", ip: ip
    machine.vm.network "public_network", bridge: 'en0: Wi-Fi (Wireless)'

    machine.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
    end

  end
end

Vagrant.configure("2") do |config|
  machines.each do |machine|
    define_machine(config, machine[:name], machine[:ip])
  end

  config.vm.provision "ansible" do |ansible|
    # We want the inventory to be created but we don't want to provision here
    # since it's slow and runs in serial. Instead we'll run a no-op playbook
    # and manually invoke ansible later.
    ansible.playbook = "provisioning/no-op.yml"

    # Define groups for our machines and make sure ansible uses python3
    # for all of the machines.
    ansible.groups = {}
    ansible.host_vars = {}
    machines.each do |machine|
      if ansible.groups[machine[:role]] == nil then
        ansible.groups[machine[:role]] = []
      end

      ansible.groups[machine[:role]].push machine[:name]

      ansible.host_vars[machine[:name]] = {
        'ansible_python_interpreter' => '/usr/bin/python3'
      }
    end
  end
end
