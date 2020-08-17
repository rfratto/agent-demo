# -*- mode: ruby -*-
# vi: set ft=ruby :

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
    machine.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
    end

    machine.vm.network "public_network"
    machine.vm.network "private_network", ip: ip
  end
end

Vagrant.configure("2") do |config|
  machines = [
    # The "team" machines will be responsible for running "team software,"
    # which (for the sake of the demo) is going to be a piece of software
    # that a team wants to monitor.
    #
    # The "team" machines will still run the Agent for collecting system
    # metrics but the software they run will be scraped by the scraping
    # server machines.
    { name: "team_a", ip: "10.0.0.2" },
    { name: "team_b", ip: "10.0.0.3" },

    # The "scraping server" machines will be responsible for running a
    # scraping service cluster to manage a significant number of scrape
    # load.
    #
    # Like the "team" machines, the "scraping server" machines will also
    # run an Agent (aside from the clustered agent) for the purposes of
    # scraping its own system metrics.
    #
    # The first scraping_server will be responsible for running ETCD.
    { name: "scraping_server_a", ip: "10.0.1.2" },
    { name: "scraping_server_b", ip: "10.0.1.3" },
    { name: "scraping_server_c", ip: "10.0.1.4" },
  ]

  machines.each do |machine|
    define_machine(config, machine[:name], machine[:ip])
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"

    ansible.groups = {
      "team" => ["team_a", "team_b"],
      "scrapers" => ["scraping_server_a", "scraping_server_b", "scraping_server_c"],
      "etcd" => ["scraping_server_a"],
    }
  end
end
