
Vagrant.configure("2") do |config|
  config.vm.box = "chef/centos-6.5"
  config.landrush.enabled = true
  config.landrush.tld = "vagrant"



  ["1", "2", "3"].each do |i|
    zk_servers_spec = "1:10.0.10.11,2:10.0.10.12,3:10.0.10.13"
    config.vm.define "zk-" + i do |cfg|
      cfg.vm.hostname = "zk-" + i + ".vagrant"
      cfg.vm.network "private_network", ip: "10.0.10.1" + i

      # create a synced folder for the exhibitor shared cfg
      # We have to use the UID, GID because synced_folder
      # will not work with a non-existant username, group
      cfg.vm.synced_folder "shared/exhibitor", "/exhibitor",
                           owner: 10002, group: 1007

    
      # create the zookeeper user and group with the UID/GID we
      # used with the synced_folder directive
      cfg.vm.provision :shell do |shell|
        shell.inline = "getent group zookeeper || groupadd -g 1007 zookeeper;
                  getent passwd zookeeper || useradd -M -u 10002 -g zookeeper zookeeper;"
      end
    
      # Provision zk and exhibitor
      cfg.vm.provision "chef_solo" do |chef|
        chef.roles_path = "roles"
        chef.add_role('zk')
        chef.json = {
          "exhibitor" => {
            "cli" => {
              "hostname" => "10.0.10.1" + i,
              "configtype" => "file",
              "fsconfigdir" => "/exhibitor"
            },
            "config" => {
              "servers_spec" => zk_servers_spec
            }
          }
        }
      end
    end
  end

  config.vm.define "mesos-master-1" do |cfg|
    cfg.vm.hostname = "mesos-master-1.vagrant"
    cfg.vm.network "public_network", ip: "192.168.20.1"
    cfg.vm.provision "chef_solo" do |chef|
      chef.roles_path = "roles"
      chef.add_role('mesos-master')
    end
  end


  config.vm.define "mesos-slave-1" do |cfg|
    cfg.vm.network "public_network", ip: "192.168.30.1"
    cfg.vm.hostname = "mesos-slave-1.vagrant"
    cfg.vm.provision "chef_solo" do |chef|
      chef.roles_path = "roles"
      chef.add_role('mesos-slave')
      chef.json = {
        "mesos" => {
          "slave" => {
            "flags" => {
              "master" => "192.168.10.2:5050"
            }
          }
        }
      }
    end
  end  
end
