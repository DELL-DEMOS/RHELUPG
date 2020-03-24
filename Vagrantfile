# -*- mode: ruby -*-
# vi: set ft=ruby :

#VAGRANTFILE_API_VERSION = "2"

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.provider :vbox do |vbox|
  config.vm.box = "jasonorona/rhel-7.6"
  config.disksize.size = '160GB'   # <--- This option depends on the plugin <vagrant plugin install vagrant-disksize>
  vbox.memory = 8192
  end

# RHEL7
  config.vm.define "rhelUPG" do |rhelUPG|
    rhel7.vm.hostname = "rhelUPG"
    rhel7.vm.box = "jasonorona/rhel-7.6"
  end

 $create_filesystem = <<SCRIPT
  parted /dev/sda mkpart primary ext4 69G 130G
  parted -s /dev/sda set 3 lvm on
  udevadm settle
  pvcreate /dev/sda3
  vgextend rhel /dev/sda3
  echo "==> SHELL PROVISIONER: Setting up logical volumes"
  lvcreate rhel -n APPLV -L 5G
  mkfs.ext4 /dev/rhel/APPLV
  mkdir -p /appfs
  echo "/dev/mapper/rhel-APPLV /appfs               ext4    errors=remount-ro 0       1" >> /etc/fstab
  mount -a
  chown vagrant:vagrant /appfs
  chmod 755 /appfs
  echo "#!/usr/bin/bash" >> /appfs/application.sh
  echo "while true;" >> /appfs/application.sh
  echo "do" >> /appfs/application.sh
  echo 'echo `date +%m-%d-%Y` >> /appfs/application_data' >> /appfs/application.sh
  echo 'echo `cat /etc/redhat-release` >> /appfs/application_data' >> /appfs/application.sh
  echo 'echo `uname -r` >> /appfs/application_data' >> /appfs/application.sh
  echo 'echo "------------------------------------------------------" >> /appfs/application_data' >> /appfs/application.sh
  echo 'sleep 15' >> /appfs/application.sh
  echo "done" >> /appfs/application.sh
  chown vagrant:vagrant /appfs/application.sh
  chmod +x /appfs/application.sh
  echo "==> This is the end of the configuration file"
 SCRIPT
 config.vm.provision "shell", inline: $create_filesystem
end
