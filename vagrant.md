# Vagrant

```
# As root
yum install ruby-devel libvirt-devel
vagrant plugin install vagrant-libvirt
mkdir vagrant; cd $_
vagrant init
vi Vagrantfile

  config.vm.provider "libvirt"

tools=/root/.vagrant.d/gems/gems/vagrant-libvirt-0.0.32/tools/

vi $tools/prepare_redhat_for_box.sh          # an idea VM OS ready for packing

cd /data/vms
vagrant box remove Centos7_Minimal
rm vagrant_base.box
$tools/create_box.sh vagrant_base.qcow2   # package image into box file
  # takes a little while
  # - copies disk images and compresses it
  # - creates vagrant_base.box

vagrant -p box add Centos7_Minimal vagrant_base.box --force
  # takes a while
  # uncompresses box into /root/.vagrant.d/boxes, ready for use


vagrant up --provider=libvirt
export VAGRANT_LOG=debug

vagrant plugin install vagrant-hostsupdater
```

New method...
```
# In a directory...
vagrant init minimal/centos7; vagrant up --provider virtualbox
# login and do yum update
ssh-keygen -y -f ~/.vagrant.d/insecure_private_key  # to get private key
# copy this over ~vagrant/.ssh/authorized_keys
vagrant package --base {vbox_name} --output myc7.box
vagrant add myc7.box
```

# Misc

## Guest additions
```
vagrant plugin install vagrant-vbguest
vagrant vbguest --status
vagrant vbguest --do install
```

