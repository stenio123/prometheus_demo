Vagrant.configure("2") do |config|
  if Vagrant.has_plugin?("vagrant-timezone")
    config.timezone.value = "America/New_York"
  end
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.network "private_network", ip: "192.168.88.88"
  # For when exposing localhost test
  config.vm.network "forwarded_port", guest: 9100, host: 9101
  # For nginx tests with mapped internal domain
  config.vm.network "forwarded_port", guest: 80, host: 8001
end