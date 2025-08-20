# Prompt for SSH key via environment variable
if ENV['VAGRANT_SSH_KEY'].nil?
  puts 'SSH key not found in environment variable VAGRANT_SSH_KEY.'
  puts 'Please set the SSH key using: export VAGRANT_SSH_KEY="ssh-rsa AAA..."'
  exit
end

Vagrant.configure("2") do |config|
  # Box from Vagrant Cloud
  config.vm.box = "zluki-windows/windows-server-2022-ssh"

  # Provider: libvirt
  config.vm.provider "libvirt" do |libvirt|
    libvirt.memory = 4096
    libvirt.cpus = 2
  end

  # disable shared folder
  config.vm.synced_folder ".", "/vagrant", disabled: true

  (1..7).each do |i|
    config.vm.define "test-#{i}" do |node|
      node.vm.network "private_network", ip: "192.168.56.#{i+50}"
      node.vm.hostname = "test-#{i}"

      # set winrm credentials
      node.vm.communicator = "winrm"
      node.winrm.username = "Administrator"
      node.winrm.password = "aim8Du7h"

      # PowerShell Provisioning: place SSH-Key
      node.vm.provision "shell", privileged: true, inline: <<-PS
        $key = "#{ENV['VAGRANT_SSH_KEY']}"
        $file = "C:\\ProgramData\\ssh\\administrators_authorized_keys"
        if (!(Test-Path -Path (Split-Path $file))) {
          New-Item -ItemType Directory -Path (Split-Path $file) -Force | Out-Null
        }
        Set-Content -Path $file -Value $key
        
        icacls $file /inheritance:r
        icacls $file /grant "Administrators:F"
        icacls $file /remove "NT AUTHORITY\\Authenticated Users"
        icacls $file /remove "NT AUTHORITY\\SYSTEM"
      PS
    end
  end

end