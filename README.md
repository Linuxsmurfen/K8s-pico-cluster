# RPI-cluster-with-K8s
What to do when you have too many RPIs lying around


```
            / --> rpi3
wifi --> rpi3 --> rpi2
            \ --> rpi4
```            
            

1. Prepare the sd-cards
    ``` sudo dd bs=4M if=2020-05-27-raspios-buster-lite-armhf.img of=/dev/sdXX status=progress conv=fsync ```
    
   - Enable ssh
   ``` touch  ../boot/ssh ```
   
   - For the router node create 
   ``` ../boot/wpa_supplicant.conf ```
   ```
   ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
   update_config=1
   country=SE

   network={
     ssid="<your ssid>"
     psk="<Password for your wireless LAN>"
   }
  ```
   
2. Router node setup

  Install Ansible
  sudo apt-get update
  sudo apt install ansible

  ssh-keygen
  ssh-copy-id localhost

  ansible-playbook 1-routersetup.yml
reboot

  cat /var/lib/misc/dnsmasq.leases

3. Cluster node setup
  ssh-copy-id pi@10.0.0.50
  ssh-copy-id pi@10.0.0.60
  ssh-copy-id pi@10.0.0.61

ansible cluster -m ping
ansible-playbook 2-clustersetup.yml
reboot


