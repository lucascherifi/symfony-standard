Vagrant.configure("2") do |config|

    config.vm.provider :virtualbox do |v|
        v.name = "SymfonyVagrant"
        v.customize ["modifyvm", :id, "--memory", 4000]
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end

    config.vm.box = "kasifi/phpbox"

    config.vm.network :private_network, ip: "192.168.33.2"
    config.ssh.forward_agent = true

    # hide "stdin: is not a tty" (@see: https://github.com/mitchellh/vagrant/issues/1673)
    config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

    # Copy host .gitconfig file to the guest
    gitconfig = Pathname.new("#{Dir.home}/.gitconfig")
    config.vm.provision :shell, :inline => "echo -e '#{gitconfig.read()}' > '/home/vagrant/.gitconfig'", privileged: false if gitconfig.exist?

    # Copy host .ssh/id_rsa file to the guest
    id_rsa = Pathname.new("#{Dir.home}/.ssh/id_rsa")
    config.vm.provision :shell, :inline => "echo -e '#{gitconfig.read()}' > '/home/vagrant/.ssh/id_rsa'", privileged: false if id_rsa.exist?

    config.vm.provision "ansible" do |ansible|

        ansible.playbook = "ansible/playbook.yml"
        ansible.inventory_path = "ansible/inventories/vm"
        ansible.limit = 'vm'
        ansible.extra_vars = {
            ansible_ssh_user: 'vagrant',
        }

        # debugging
        #ansible.verbose = 'vvvv'
        #ansible.start_at_task = 'phpcommon'
    end

    # NFS shares
    config.vm.synced_folder ".", "/var/www", type: "nfs" # project
    config.vm.synced_folder "~/.composer", "/home/vagrant/.composer", type: "nfs" # composer dependencies
    config.vm.synced_folder "~/.vagrant-apt-archives", "/var/cache/apt/archives", type: "nfs" # apt cache archives
end
