## Virtualisation
```sh
egrep 'vmx|svm' /proc/cpuinfo   # check cpu support (vmx=intel, svm=amd)
lsmod | grep kvm     # should see, kvm and kvm_intel (or amd)
 # if kvm_intel not there, need to enable intel virtualisation in BIOS

systemctl status libvirtd
  # generic virtualisation  interface, under
  # virt-manager (GUI), virt-install & virsh

virbr0      # virtual bridge (network)
vnet0       # host device which connects to virt NIC on a VM

virt-install \
  -n myname \
  -r 512 \
  --disk /data/vm/disk1.img,size=8 \
  -l /data/images/CentOS-7-x86_64-Minimal-1503-01.iso \
  --vcpus 2 \
  -w default \
  --graphics none \
  --extra-args 'ks=file:/ks.cfg console=ttyS0,115200n8 serial'  \
  --initrd-inject=/home/john/Documents/ks.cfg \

  # --noautoconsole \       # doesn't automatically connect to console after create
  # --os-variant=rhel7 \    # highly recommended for performance

virsh net-list              # list networks
virsh net-edit {network}    # edit xml file for network (contains DHCP settings)
virsh net-destroy {network}
/etc/libvirt/qemu/          # xml file for each VM
virsh edit {vm}         # edit xml file safely
/var/lib/libvirt/dnsmasq/default.leases   # ip leases

virsh destroy vm1       # immediately terminate (still exists!)
virsh list --all        # list all "domains" (vms), on or off
virsh start {vm}
/var/log/libvirt/qemu   # contains a log for each vm
virsh undefine vm1      # remove if not running, convert to transient if running
virsh vol-list default  # list vols in default pool
virt-clone --prompt     # interactively clone

guestmount -a /var/lib/libvirt/images/vm1.img -i --ro /mnt
  # mount guest disk fs on /mnt

Serial console with KVM guest, need these in /etc/default/grub:
GRUB_TERMINAL_OUTPUT="serial"
GRUB_TERMINAL=serial
GRUB_CMDLINE_LINUX="… console=ttyS0,115200n8 console=ttyS0"

```

