# WIP
## Topology
```
[============]       [===========]
[=CONTROLLER=] ----> [==SERVER1==]
[============]       [===========]
```

## Step dibawah mulai dari controller dulu
1. Biar VMnya ada internetnya dibikin 2 network adapter jangan ganti NATnya ke LAN segment

2. Buat clone repository ini, install git dulu:
```
apt install git -y
```

3. Clone repository ini pake perintah:
```
git clone https://github.com/teknikkulijawa/UPASJ
```

4. Ganti nama folder UPASJ ini ke workfolder
```
mv UPASJ workfolder
```

5. Bisa cek catatan ini juga di foldernya dengan cara:
```
cd workfolder
nano README.md
```

6. Edit file `/etc/network/interfaces` di server1
```
nano /etc/network/interfaces
```
```
auto ens36
iface ens36 inet static
      address 192.168.69.1/24
```

7. Edit file `/etc/network/interfaces` di controller
```
nano /etc/network/interfaces
```
```
auto ens36
iface ens36 inet static
      address 192.168.69.2/24
```

8. Install Ansible di controller
```
root@controller:~# apt install ansible sshpass -y
```

9. SSH ke server1 terus kemudian yes
```
root@controller:~# ssh 192.168.69.1
The authenticity of host '192.168.69.1' can't be established.
ED25519 key fingerprint is:
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
root@server1:~# exit
```

10. Jalankan ansible-playbook di controller:
```
root@controller:~# ansible-playbook -i hosts create_100_users.yaml
root@controller:~# ansible-playbook -i hosts dns_server.yaml
```
