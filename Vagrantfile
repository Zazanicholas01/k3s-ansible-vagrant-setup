require "fileutils"

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.boot_timeout = 600

  config.vm.define "controlplane" do |cp|
    cp.vm.hostname = "controlplane"
    cp.vm.network "private_network", ip: "192.168.56.10"

    cp.vm.provider "virtualbox" do |vb|
      vb.name = "controlplane"
      vb.cpus = 6
      vb.memory = 12288

      disk_dir = File.join(Dir.pwd, ".vagrant", "disks")
      disk_path = File.join(disk_dir, "controlplane-data.vdi")

      FileUtils.mkdir_p(disk_dir)

      unless File.exist?(disk_path)
        vb.customize ["createhd", "--filename", disk_path, "--size", 122880]
      end

      vb.customize [
        "storageattach", :id,
        "--storagectl", "SCSI",
        "--port", 2,
        "--device", 0,
        "--type", "hdd",
        "--medium", disk_path
      ]
    end

    cp.vm.provision "ansible_local" do |a|
      a.playbook = "/vagrant/ansible/site.yaml"
      a.install = true
      a.install_mode = "pip"
      a.tags = ["addons"]
      a.raw_arguments = ["--vault-password-file=/vagrant/ansible/.vault_pass"]
      a.extra_vars = {
        "node_role" => "server",
        "control_plane_ip" => "192.168.56.10",
        "cilium_install" => true,
        "longhorn_install" => true,
        "cert_manager_install" => true,
        "nginx_gateway_install" => true,
        "flux_install" => false
      }
    end
  end
end
