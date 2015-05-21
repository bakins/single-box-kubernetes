# -*- mode: ruby -*-
# # vi: set ft=ruby :

require 'fileutils'
require 'erb'

Vagrant.require_version ">= 1.6.0"
CONFIG = File.join(File.dirname(__FILE__), "config.rb")

# Defaults for config options defined in CONFIG
$num_instances = 1
$update_channel = "alpha"
$vm_memory = 1024
$vm_cpus = 1
$forwarded_ports = { 8080 => 8080 }
$kubernetes_version = "0.17.0"
$portal_net = "10.200.0.0/16"
$cluster_cidr = "10.1.0.0/16"
$hostname = "coreos-k8s"
$ip = "172.17.8.100"

if File.exist?(CONFIG)
  require CONFIG
end

def vm_memory
  $vb_memory.nil? ? $vm_memory : $vb_memory
end

def vm_cpus
  $vb_cpus.nil? ? $vm_cpus : $vb_cpus
end

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

  config.vm.provider :virtualbox do |v|
    # On VirtualBox, we don't have guest additions or a functional vboxsf
    # in CoreOS, so tell Vagrant that so it can be smarter.
    v.check_guest_additions = false
    v.functional_vboxsf     = false
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  config.vm.hostname = $hostname

  $forwarded_ports.each do |guest, host|
	config.vm.network "forwarded_port", guest: guest, host: host, auto_correct: true
  end

  ["vmware_fusion", "vmware_workstation"].each do |vmware|
    config.vm.provider vmware do |v|
      v.vmx['memsize'] = vm_memory
      v.vmx['numvcpus'] = vm_cpus
    end
  end

  config.vm.provider :virtualbox do |vb|
    vb.memory = vm_memory
    vb.cpus = vm_cpus
  end


  config.vm.network :private_network, ip: $ip

  data = ERB.new(IO.read("provision.erb")).result()

  config.vm.provision "shell", inline: <<SCRIPT
cat << 'EOC' > /tmp/provision.sh
#{data}
EOC
sudo bash /tmp/provision.sh
SCRIPT


end
