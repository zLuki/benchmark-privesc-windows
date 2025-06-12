# Prompt for SSH key via environment variable
if ENV['VAGRANT_SSH_KEY'].nil?
  puts 'SSH key not found in environment variable VAGRANT_SSH_KEY.'
  puts 'Please set the SSH key using: export VAGRANT_SSH_KEY="ssh-rsa AAA..."'
  exit
end

Vagrant.configure("2") do |config|
  # Box from Vagrant Cloud
  config.vm.box = "valengus/windows-2022-standard-core"

  # provider: virtualbox
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"       # RAM (kann angepasst werden)
    vb.cpus = 2              # Anzahl CPUs
  end

  # disable shared folder
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # network: static ip
  config.vm.network "private_network", ip: "192.168.56.50"

  # install and configure ssh for Administrator login over key
  config.vm.provision "shell", privileged: true, inline: <<-PS
    # install ssh server
    Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0 

    # start ssh service
    Start-Service -Name "sshd" 

    # set startup type automatic
    Set-Service -Name "sshd" -StartupType Automatic 

    # Add authorized key
    $keyPath = "C:\\ProgramData\\ssh\\administrators_authorized_keys"
    $key = "#{ENV['VAGRANT_SSH_KEY']}"

    Set-Content -Path $keyPath -Value $key -Encoding ascii

    # set correct file privileges
    icacls $keyPath /inheritance:r
    icacls $keyPath /remove:g "Users"
    icacls $keyPath /remove:g "Everyone"
    icacls $keyPath /grant:r "SYSTEM:F"
    icacls $keyPath /grant:r "Administrators:F"
    icacls $keyPath /grant:r "Administrator:F"

    # restart ssh service
    Restart-Service sshd
  PS

  # change standard shell to powershell
  config.vm.provision "shell", privileged: true, inline: <<-PS
    $shellParams = @{
        Path         = 'HKLM:\\SOFTWARE\\OpenSSH'
        Name         = 'DefaultShell'
        Value        = 'C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe'
        PropertyType = 'String'
        Force        = $true
    }
    New-ItemProperty @shellParams
  PS

end