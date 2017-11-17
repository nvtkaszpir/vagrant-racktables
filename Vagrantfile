V_CPU = 2 # in cores
V_MEM = 1024 # in megabytes per core
V_MEM_TOTAL = V_MEM * V_CPU
SYNC_TYPE = "rsync" # how to sync files in vagrant, for lxc rsync is suggested

Vagrant.configure(2) do |config|
  # because sometimes vagrant key insertion in boxes is broken, we skip step of updating insecure keys
  config.ssh.insert_key = false

  config.vm.provider :libvirt do |libvirt, override|
    override.vm.box = "generic/ubuntu1604"
    # this is vagrant, vm is disposable, so set up supper agressive disk access
    libvirt.memory = V_MEM
    libvirt.cpus = V_CPU
    libvirt.volume_cache = 'unsafe'
    libvirt.video_type = 'vga'
    libvirt.video_vram = '16384'
  end

  config.vm.provider :lxc do |lxc, override|
    override.vm.box = "st01tkh/xenial64-lxc"
    lxc.customize "aa_allow_incomplete", "1"
    lxc.customize "cgroup.cpuset.cpus", "0,1"  # two cores
    lxc.customize "cgroup.memory.limit_in_bytes", "#{V_MEM_TOTAL}M"
  end

  config.vm.provider :virtualbox do |vbox, override|
    override.vm.box = "generic/ubuntu1604"
  end


  config.vm.define "racktables" do |v|
    # default vm overrides go here
  end

  # we assume ubuntu/debian, install python on guest, required for ansible
  config.ssh.shell = "sudo apt-get update && sudo apt-get install -y python"

  # needed to install stuff like http proxy, system update, install tools etc
  config.vm.provision "dev", type: 'ansible' do |ansible|
    ansible.limit = "all"
    ansible.playbook = "ansible/local_dev.yml"
  end

  # app stack core, web and db
  config.vm.provision "sys", type: 'ansible' do |ansible|
    ansible.limit = "all"
    ansible.playbook = "play_sys.yml"
    ansible.groups = {
      "web" => ["racktables"],
      "db" => ["racktables"],
      "app" => ["racktables"],
      "all_groups:children" => ["web", "db", "app"],
    }
    ansible.galaxy_role_file = 'requirements.yml'
    ansible.galaxy_roles_path = '.galaxy'
  end

  # app specific playbook
  config.vm.provision "app", type: 'ansible' do |ansible|
    ansible.limit = "all"
    ansible.playbook = "play_racktables.yml"
    ansible.groups = {
      "web" => ["racktables"],
      "db" => ["racktables"],
      "app" => ["racktables"],
      "all_groups:children" => ["web", "db", "app"],
    }
  end

end