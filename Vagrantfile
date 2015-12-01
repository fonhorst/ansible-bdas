# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # for building we use the same files as for the regular run
  config.ssh.forward_agent = true
  config.ssh.insert_key = false
  config.vm.synced_folder ".", "/home/vagrant/project", :mount_options => ["dmode=777","fmode=666"]
	
  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://proxy.ifmo.ru:3128/"
    config.proxy.no_proxy = "localhost,127.0.0.1,"
  end

  # read data for vagrant run
  nds = File.open("./ansible-bdas/hosts.backup","r") do |hosts|
	nodes = {}
	while(line = hosts.gets) do
		if line.start_with?("###")
			break
		end
		strs = line.strip().split(" ")
		params = {}
		for s in strs[1..-1] do
			pair = s.split("=")
			params[pair[0]]=pair[1]
		end
		nodes[strs[0]] = params
	end
	nodes
  end

  # ubuntu
  nds.keys.sort.each.map do |name|
    ip = nds[name]["ansible_ssh_host"]

    config.vm.define name, primary: true do |c|
       c.vm.network "private_network", ip: ip
   #   c.vm.network "public_network", ip: ip
   #   c.vm.network "forwarded_port",  guest: 22,  host: 22,  id: "ssh",  auto_correct: true
      c.vm.network :forwarded_port, guest: 5050, host: 5050, auto_correct: true
      c.vm.network :forwarded_port, guest: 50070, host: 50070, auto_correct: true
      c.vm.network :forwarded_port, guest: 9000, host: 9000, auto_correct: true
   #   c.vm.network :forwarded_port, guest: 5051, host: 5051, auto_correct: true
   #   c.ssh.forward_agent = true

      #c.vm.box = "build/mesos-ubuntu"
      c.vm.box = "ubuntu/trusty64"
      #c.vm.box = "ubuntu-trusty64"
      c.vm.hostname = name
    c.vm.provision "shell" do |s|
        #dns_server = "if ! grep -q \'nameserver 192.168.13.132\' /etc/resolvconf/resolv.conf.d/head; then echo 'nameserver 192.168.13.132'|tee --append /etc/resolvconf/resolv.conf.d/head; fi;resolvconf -u;"
        dns_server = ""
        #default_iface = "ip route change to default dev eth1;"
        default_iface = ""
        hosts_file = "echo '127.0.0.1 localhost'|tee /etc/hosts;echo '#{ip} #{name}'|tee --append /etc/hosts;"
        #mount_devfiles = "apt-get install cifs-utils;mount -t cifs //192.168.1.225/rendler -o username=nano,password=Yt1NyDpQNm,noperm ./devfiles;"
        #s.inline = "#{hosts_file}#{dns_server}#{default_iface}apt-add-repository ppa:ansible/ansible -y; apt-get update -y; apt-get install ansible -y;"
        #s.inline = "#{hosts_file}#{dns_server}#{default_iface}"

        # TODO: remove this hack later
        hack_hosts_file_1 = "echo '192.168.92.11 node-92-11'|tee --append /etc/hosts;"
        hack_hosts_file_2 = "echo '192.168.92.12 node-92-12'|tee --append /etc/hosts;"
        hack_hosts_file_3 = "echo '192.168.92.13 node-92-13'|tee --append /etc/hosts;"

        s.inline = "#{hosts_file}#{dns_server}#{default_iface}#{hack_hosts_file_1}#{hack_hosts_file_2}#{hack_hosts_file_3}"
        s.privileged = true
    end

      #c.vm.provision "shell", path: "install-mesos-python-binding.sh"
      c.vm.provision "shell", path: "install-sshkey.sh"

      c.vm.provider :virtualbox do |vb|
      	vb.memory = 2048
	    vb.cpus = 2
      end
      c.vm.synced_folder "hdfs_data/data_#{name}", "/hdfs_data", create: true, mount_options: ["dmode=777,fmode=777"]

    end
  end
end

