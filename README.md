# vagrant-node

Deploy a vagrant node in a virtual private cloud

## Virtual Private Cloud

A Virtual Private Cloud (VPC) lets you provision a logically isolated collection of systems on a private network with public access via a network gateway.

`vagrant-node` is a companion project to [alpine-router](https://github.com/bobfraser1/alpine-router) which provides the network gateway. `alpine-router` must be deployed, configured, and running before provisioning with `vagrant-node`.

## Private network provisioning

vagrant-node is essentially a Vagrantfile that overrides commonly used Vagrant defaults and provisions a node on a private network _without_ a public interface.

This project uses [Alpine linux](https://www.alpinelinux.org) as an example but should work with any properly configured [Vagrant Base Box](https://www.vagrantup.com/docs/boxes/base).

## Node configuration

**Before you begin:** As part of deploying `alpine-router`, a host-only network is created. You will need the name and IP address range of the host-only network.

**Configuration:** Edit the `Vagrantfile`. Update the following variables:

    VAGRANT_BOX = vagrant base box to deploy
    VM_HOSTNAME = hostname for the guest OS
    VM_NETWORK = name of the VirtualBox host-only network
    VM_MAC = interface MAC address
    VM_IP = IP address
    VM_MEMORY = amount of VM memory
    VM_CPUS = number of CPUs

## Router configuration

Configure `alpine-router` so DHCP will assign the static IP to the node.

Format the MAC address as an ethernet address. Example:

    080027000010 → 08:00:27:00:00:10

SSH into your alpine router and edit `/etc/dnsmasq.conf`. Add a `dhcp-host` entry with the ethernet address, hostname, and IP address. Example:

    dhcp-host=08:00:27:00:00:10,alpine1,192.168.60.10,infinite

Restart `dnsmasq`:

    sudo /etc/init.d/dnsmasq restart

## Deployment

    vagrant up

will provision the vagrant node configured with the static IP address and gateway access to the public network.

## Testing

SSH into the node:

    vagrant ssh

Use `ping` of a public host such as google.com to ensure public network access. Use `ip addr show` to see that the correct IP address was assigned. Use `nslookup` to see that the nameserver returns the proper FQDN:

    alpine1:~$ nslookup alpine1
    Server:     192.168.60.2
    Address:    192.168.60.2:53

    Name:    alpine1.example.com
    Address: 192.168.60.10

## Troubleshooting

- `Host appears down. Retrying...` - check the VirtualBox console and make sure the static address was assigned and not a random DHCP address. Check your router and node configuration.
- Nameserver issues - If you are using a modern Linux box such as a recent `debian` or `ubuntu`, DHCP may not properly set up the nameserver. You can still reach local nodes via IP address and if you don't care about local name resolution then: `¯\_(ツ)_/¯` Don't worry! Otherwise, ssh into your node and check `/etc/resolve.conf` for clues. Your system configuration may be controlled by management tools such as `resolvconf` or `netplan`. Solutions are guest OS dependent.

## Next steps

Once the `vagrant-node` is successfully deployed, you can add to the `Vagrantfile` to extend the workflow with [Vagrant provisioning](https://www.vagrantup.com/docs/provisioning). You can even provision a multi-node cluster with [Vagrant Multi-Machine](https://www.vagrantup.com/docs/multi-machine). You can also deploy a wide variety of Boxes hosted on [Vagrant Cloud](https://app.vagrantup.com/).
