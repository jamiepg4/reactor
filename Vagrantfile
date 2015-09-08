# -*- mode: ruby -*-
# # vi: set ft=ruby :

require 'fileutils'

Vagrant.require_version ">= 1.6.0"

# Defaults for config options
$update_channel = "stable"
$vb_gui = false
$vb_memory = 1024
$vb_cpus = 1
$vb_hostname = "reactor.dev"
$vb_ip = "192.168.50.11"

Vagrant.configure("2") do |config|
  # always use Vagrants insecure key
  config.ssh.insert_key = false

  config.vm.box = "coreos-%s" % $update_channel
  config.vm.box_version = ">= 308.0.1"
  config.vm.box_url = "http://%s.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json" % $update_channel

  ["vmware_fusion", "vmware_workstation"].each do |vmware|
    config.vm.provider vmware do |v, override|
      override.vm.box_url = "http://%s.release.core-os.net/amd64-usr/current/coreos_production_vagrant_vmware_fusion.json" % $update_channel
    end
  end

  config.vm.provider :virtualbox do |vb|
    # On VirtualBox, we don't have guest additions or a functional vboxsf
    # in CoreOS, so tell Vagrant that so it can be smarter.
    vb.check_guest_additions = false
    vb.functional_vboxsf     = false

    vb.gui = $vb_gui
    vb.memory = $vb_memory
    vb.cpus = $vb_cpus
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end

  if Vagrant.has_plugin?("vagrant-hostsupdater")
    config.hostsupdater.remove_on_suspend = true
  end

  config.vm.hostname = $vb_hostname
  config.vm.network :private_network, ip: $vb_ip

  config.vm.synced_folder "./config", "/home/core/config", create: true, id: "core", mount_options: ['nolock,vers=3,udp']
  config.vm.synced_folder "./theme", "/home/core/theme", create: true, id: "theme", mount_options: ['nolock,vers=3,udp']

  # Provisioning scripts to conditionally run on "vagrant provision"
  config.vm.provision :shell, privileged: false, inline: <<-EOS
    docker pull fusionengineering/reactor:latest
  EOS

  # Provisioning scripts needing to always be run
  config.vm.provision :shell, run: "always", inline: <<-EOS
    docker run -d -p 80:80 -p 3306:3306 -v /home/core/theme:/srv/www/reactor.dev/wp-content/themes/fusion-theme fusionengineering/reactor
  EOS

end
