VAGRANT_BOX = 'bobfraser1/alpine315'
VM_HOSTNAME = 'alpine1'
VM_NETWORK = 'vboxnet1'
VM_MAC = '080027000010'
VM_IP = '192.168.60.10'
VM_MEMORY = '2048'
VM_CPUS = '2'

Vagrant.configure('2') do |config|
  config.vm.box = VAGRANT_BOX
  config.vm.hostname = VM_HOSTNAME
  config.vm.network 'private_network',
                    name: VM_NETWORK,
                    mac: VM_MAC,
                    ip: VM_IP,
                    adapter: 1,
                    auto_config: false
  config.ssh.host = VM_IP
  config.vm.provider 'virtualbox' do |vm|
    vm.name = VM_HOSTNAME
    vm.memory = VM_MEMORY
    vm.cpus = VM_CPUS
  end
end
