# root-of-rs
**t(rust)** stack: firmware + hypervisor 🦀 

this repo will be for bootstrapping nested vms using a pure rust stack:

- crosvm 
- firecracker
- rust-vmm
- cloud-hypervisor


### quickstart:

guest img, fw, hypervisor binaries
```bash 
wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img
qemu-img convert -p -f qcow2 -O raw noble-server-cloudimg-amd64.img noble-server-cloudimg-amd64.raw
wget https://github.com/cloud-hypervisor/rust-hypervisor-firmware/releases/download/0.4.2/hypervisor-fw
wget https://github.com/cloud-hypervisor/cloud-hypervisor/releases/download/v41.0/cloud-hypervisor-static
sudo setcap cap_net_admin+ep cloud-hypervisor-static
```
cloud-init fix
```bash
rm -f /tmp/ubuntu-cloudinit.img
mkdosfs -n CIDATA -C /tmp/ubuntu-cloudinit.img 8192
mcopy -oi /tmp/ubuntu-cloudinit.img -s fixture/cloud-init/ubuntu/local/user-data ::
mcopy -oi /tmp/ubuntu-cloudinit.img -s fixture/cloud-init/ubuntu/local/meta-data ::
mcopy -oi /tmp/ubuntu-cloudinit.img -s fixture/cloud-init/ubuntu/local/network-config ::
```
boot
```bash
./cloud-hypervisor-static \
	--kernel ./hypervisor-fw \
	--disk path=noble-server-cloudimg-amd64.raw path=/tmp/ubuntu-cloudinit.img \
	--cpus boot=4 \
	--memory size=1024M \
	--net "tap=,mac=,ip=,mask="
 ```
