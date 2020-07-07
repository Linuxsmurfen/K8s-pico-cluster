# RPI-cluster-with-K8s
What to do when you have too many RPIs lying around and want to learn kubernetes and ansible?  
  
Build a kubernetes cluster using rpi with ansible!  

The cluster contains of one rpi that acts as the router while the other three builds the cluster.  
The setup of the router and cluster nodes are done via Ansible playbooks.  
  
  
Inspired by..  
https://www.shogan.co.uk/kubernetes/building-a-raspberry-pi-kubernetes-cluster-part-1-routing/  
https://downey.io/blog/create-raspberry-pi-3-router-dhcp-server/  
http://www.lpenz.org/articles/ansiblerpi/index.html  


## Connect everything
One RPI will be the router that is connected via wifi to the network/internet.   
All RPIs are connected to a isolated/dedicated switch that will handle the traffic between the nodes.    
```
            / --> rpi3
wifi --> rpi3 --> rpi2
            \ --> rpi4
```


## Prepare the sd-cards
- Create the sd cards  
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
   
## Router node setup

Install Ansible  
sudo apt-get update  
sudo apt install ansible  

ssh-keygen  
ssh-copy-id localhost  

ansible-playbook 1-routersetup.yml  
reboot  

cat /var/lib/misc/dnsmasq.leases  

## Cluster node setup

Copy ssh keys to the cluster nodes  
```
ssh-copy-id pi@10.0.0.50
ssh-copy-id pi@10.0.0.60
ssh-copy-id pi@10.0.0.61
```  

ansible cluster -m ping  
ansible-playbook 2-clustersetup.yml  
reboot  


