Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-18.04"


  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080

 
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  #    #apt-get install -y grub-common
  #apt-get install -y grub-pc-bin
  #     apt-get install -y xorriso

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y binutils
    apt-get install -y cgdb
    apt-get install -y cmake
    apt-get install -y gcc
    apt-get install -y gdb
    apt-get install -y git
    apt-get install -y qemu
    apt-get install -y autoconf
    apt-get install -y wget
    apt-get install -y python3.6
    apt-get install -y nasm
 
  SHELL
end
