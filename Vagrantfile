# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :centos7 => {
        :box_url => 'http://packages.vstone.eu/vagrant-boxes/centos/7.x/centos-7.x-64bit-puppet-3.x-vbox.4.3.14-1.box',
        :ip_addr => '192.168.11.101',
        :offset => 0,
  },
  :centos6 => {
        :box_url => 'http://packages.vstone.eu/vagrant-boxes/centos-6.x-64bit-latest.box',
        :ip_addr => '192.168.11.102',
        :offset  => 10,
  },
  :debian7 => {
        :box_url => 'http://puppet-vagrant-boxes.puppetlabs.com/debian-73-x64-virtualbox-nocm.box',
        :ip_addr => '192.168.11.122',
        :offset  => 100,
  },
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          offset = boxconfig[:offset]
          box.vm.box = boxname.to_s
          box.vm.box_url= boxconfig[:box_url]
          box.vm.host_name = boxname.to_s

          box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "1024"]
            # uncomment this for GUI
            # vb.gui = true
          end

          box.vm.provision :shell do |s|
             s.inline = 'mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh'
          end

          box.vm.provision :ansible do |ansible|
            ansible.playbook = "provisioning/site.yml"
            ansible.host_key_checking = false
            ansible.verbose = "v"
            ansible.groups = {
                'iscsi-target' => MACHINES.keys,
                'iscsi-initiator' => MACHINES.keys,
            }
            # if you want to fire ansible on all machines at parallel, use this!
            #ansible.limit = 'all'
          end
      end
  end
end

