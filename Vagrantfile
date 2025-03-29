# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # GitLab 서버 VM 설정
  config.vm.define :gitlab do |cfg|
    cfg.vm.box = "bento/ubuntu-22.04"
    cfg.vm.box_version = "202502.21.0"
    cfg.vm.hostname = "gitlab.local"

    cfg.vm.network "forwarded_port", guest: 80, host: 8082
    cfg.vm.network "forwarded_port", guest: 22, host: 8023
    cfg.vm.network "private_network", ip: "192.168.33.44"
    cfg.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/"]

    cfg.vm.provider "parallels" do |prl|
      prl.name = "gitlab.local"
      prl.memory = "4096"
    end

    cfg.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y curl openssh-server ca-certificates

      debconf-set-selections <<< "postfix postfix/mailname string $HOSTNAME"
      debconf-set-selections <<< "postfix postfix/main_mailer_type string 'Internet Site'"
      DEBIAN_FRONTEND=noninteractive sudo apt-get install -y postfix
      sudo apt-get update
      sudo apt-get install -y libatomic1

      if [ ! -e /gitlab-ce.deb ]; then
         wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/jammy/gitlab-ce_17.10.0-ce.0_arm64.deb/download.deb
      fi

      sudo dpkg -i gitlab-ce_17.10.0-ce.0_arm64.deb
      sudo gitlab-ctl reconfigure
    SHELL
  end  # GitLab 서버 VM 끝

  # # GitLab Runner VM 설정
  # config.vm.define :runner do |cfg|  
  #   cfg.vm.box = "bento/ubuntu-22.04"
  #   cfg.vm.box_version = "202502.21.0"
  #   cfg.vm.hostname = "runner.local"
  #   cfg.vm.network "private_network", ip: "192.168.33.45"  

  #   cfg.vm.provider "parallels" do |prl| 
  #     prl.gui = true 
  #     prl.name = "gitlab-runner.local"
  #     prl.memory = "2048"
  #   end

  #   cfg.vm.provision "shell", inline: <<-SHELL
  #     sudo apt-get update
  #     sudo apt-get install -y curl

  #     # GitLab Runner 설치
  #     sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm64
  #     sudo chmod +x /usr/local/bin/gitlab-runner

  #     # GitLab Runner 사용자 추가
  #     sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

  #     # GitLab Runner 서비스 등록 및 시작
  #     sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
  #     sudo gitlab-runner start
  #   SHELL
  # end  # GitLab Runner VM 끝
end

