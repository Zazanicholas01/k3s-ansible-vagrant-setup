Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/jammy64"

    config.vm.define "controlplane" do |cp|
        cp.vm.hostname = "controlplane"
        cp.vm.network "private_network", ip: "192.168.56.10"
        cp.vm.provider "virtualbox" do |vb|
            vb.name = "controlplane"
            vb.cpus = 4
            vb.memory = 10240
        end
    
        cp.vm.provision "ansible_local" do |a|
            a.playbook = "/vagrant/ansible/site.yaml"
            a.install = true
            a.install_mode = "pip"
            a.extra_vars = {
                "node_role" => "server",
                "control_plane_ip" => "192.168.56.10"
            }
        end
    end
end
