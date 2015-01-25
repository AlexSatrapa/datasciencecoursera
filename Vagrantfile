# -*- mode: ruby -*-
# vi: set ft=ruby :

# http://stackoverflow.com/questions/42566/getting-the-hostname-or-ip-in-ruby-on-rails
require 'socket'
def local_ip
	orig, Socket.do_not_reverse_lookup = Socket.do_not_reverse_lookup, true	 # turn off reverse DNS resolution temporarily

	UDPSocket.open do |s|
		s.connect '8.8.8.8', 1
		s.addr.last
	end
ensure
	Socket.do_not_reverse_lookup = orig
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
	# Every Vagrant virtual environment requires a box to build off of.
	config.vm.provider 'vmware_fusion' do |provider, override|
		override.vm.box = "precise64_vmware"
		override.vm.box_url = "http://files.vagrantup.com/precise64_vmware.box"
	end
	config.vm.provider 'virtualbox' do |provider, override|
		override.vm.box = "precise64"
		override.vm.box_url = "http://files.vagrantup.com/precise64.box"
	end
	config.vm.hostname = "datascience"

	config.vm.network :private_network, type: :dhcp

	# By default, Ubuntu will issue complaints about STDOUT not being a TTY.
	# Turn this off to reduce the number of warnings issued by Vagrant
	config.vm.provision :shell, :privileged => false, :path => "bin/vagrant_disable_notty_warning"
	# Add a hosts entry for the proxy server, which is the VM host, which is the default gateway
	config.vm.provision :shell, :path => 'bin/vagrant_add_proxy_host', :args => [ local_ip ]
	# Upgrade all packages to current (we're using a fairly old base install)
	config.vm.provision :shell, :inline => "/usr/bin/apt-get -qqy update"

	# Provision using Puppet
	config.vm.provision :shell, :path => 'bin/vagrant_install_puppet_modules'
	config.vm.provision :puppet do |puppet|
		puppet.module_path = "puppet/modules"
		puppet.manifests_path = "puppet/manifests"
		puppet.manifest_file	= "site.pp"
	end

	# Use vagrant-proxyconf if available
	# http://tmatilai.github.io/vagrant-proxyconf/
	if Vagrant.has_plugin?("vagrant-proxyconf")
		config.proxy.http = "http://proxy:3128/"
		config.proxy.https = "http://proxy:3128/"
		config.proxy.no_proxy = "localhost,127.0.0.1,.example.com"
		config.apt_proxy.http = "http://proxy:3128/"
		config.apt_proxy.https = "DIRECT"
	end
end
