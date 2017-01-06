# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.define "git-host" do |githost|
    githost.vm.box = "centos/6"
    githost.vm.box_check_update = false
    githost.vm.network "private_network", ip: "192.168.56.40"
    githost.vm.hostname = "git-host"
    githost.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory = "2048"
    end
    githost.vm.provision "shell", inline: <<-SHELL
      yum -y install curl openssh-server openssh-clients postfix cronie
      chkconfig postfix on
      service postfix start
      lokkit -s http -s ssh
      curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | bash
      yum -y install gitlab-ce
      gitlab-ctl reconfigure
      echo "$(hostname) up"
    SHELL
  end
  config.vm.define "runner1" do |runner1|
    runner1.vm.box = "centos/7"
    runner1.vm.box_check_update = false
    runner1.vm.network "private_network", ip: "192.168.56.41"
    runner1.vm.hostname = "runner1"
    runner1.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory = "512"
    end
    runner1.vm.provision "shell", inline: <<-SHELL
      curl -sSL https://get.docker.com/ | sh
      systemctl enable docker.service
      systemctl start docker
      echo -e "\n192.168.56.40 git-host\n" >> /etc/hosts
      curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh |  bash
      yum -y install gitlab-ci-multi-runner
      echo "$(hostname) up"
    SHELL
  end
end
