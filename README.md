# HowTos
Just a rando collection of HowToDo's since I work with many different platforms and languages.


Kali export NFS for vsphere
```
mkdir /mnt/share
echo '/mnt/share *(rw,sync)' > /etc/export
service restart rpcbind
systemctl restart nfs-kernel-server
```
In vSphere add /mnt/share and server IP

Powershell manage USB devices

```
#Get the Generic USB Device whre the status is OK
$((Get-PnpDevice).where({ $_.FriendlyName -match  'Generic USB' -AND $($_.Status -eq 'OK') })
```
