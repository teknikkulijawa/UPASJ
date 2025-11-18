# WIP
## Topology
```
[============]       [===========]
[=CONTROLLER=] ----> [==SERVER1==]
[============]       [===========]
```

## Step dibawah mulai dari controller dulu
1. Buat clone repository ini, install git dulu:
`apt install git -y`

2. Clone repository ini pake perintah:
`git clone https://github.com/teknikkulijawa/UPASJ`

3. Ganti nama folder UPASJ ini ke workfolder
`mv UPASJ workfolder`

4. Bisa cek catatan ini juga di foldernya dengan cara:
```
cd workfolder
nano README.md
```

5. Edit file `/etc/network/interfaces` di server1
`nano /etc/network/interfaces`

```
auto ens36
iface ens36 inet static
      address 192.168.69.1
```

6. Edit file `/etc/network/interfaces` di controller
`nano /etc/network/interfaces`

```
auto ens36
iface ens36 inet static
      address 192.168.69.2
```

7. Install Ansible di controller
`root@controller:~# apt install ansible sshpass -y`

8. Run playbook on controller with

```
root@controller:~# ansible-playbook -i hosts create_300_users.yaml
root@controller:~# ansible-playbook -i hosts dns_server.yaml
```

Make sure FTP can login using users created by ansible and read/write into the nested
directory. You can use FileZilla program on Operator.

```
ops@operator:~$ sudo apt install filezilla
```
