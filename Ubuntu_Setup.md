<h1>Ubuntu Server Setup</h1>

<h2>Description</h2>
Not really an intended part of my Active Directory setup but it took some troubleshooting to get this setup and added to the domain. I figured it would be good to add to the network.
<br />

<h2>Setup</h2>
To create the Ubuntu image I used the preconfigured image from the Raspberry Pi image. This ensured that the ISO used was compatiable with the hardware.
<br />
<h3>Network Configuration</h3>

By default, Ubuntu comes configured not to connect to the network. In newer versions of Ubuntu, ifconfig is not preinstalled. <br />
Use: 'ip addr show' to view whether you network interface is up. <br />
If not, use: 'sudo ip link set <network_interface> up'. <br />
<p align="center">
Run 'ip addr show' once more and it should look like this:
<img src="/imgs/network_up.png"/>
Once this is done you can use: 'dchpcd network_interface' to request an new IP Address <br />
Now let's configure a static IP address by editing the yaml file in /etc/netplan/ <br />
It should look something like this the addresses portion should contain your perferred IP address and subnet mask <br />
The via portion under route should contain your default gateway IP address:
<img src="/imgs/netplan.png"/>
Now we can run 'sudo netplan apply' to apply the changes and 'ip a' to confirm that they have been made:
<img src="/imgs/netplan_apply.png"/>
</p>
<h2>Join to the Domain</h2>
To join to the domain we first need to ensure that all the packages are up-to-date with 'sudo apt update && sudo apt upgrade' <br />
<p align="center">
Then we need to install the following: 'sudo apt -y install realmd sssd sssd-tools libnss-sss libpam-sss adcli samba-common-bin oddjob oddjob-mk' <br />
<img src="/imgs/install_pkgs.png"/>
Once that's done we need to reconfigure the hostname of the server to match the domain. In my case: 'hostnamectl set-hostname ubuntu.ocasiosec.com'
<img src="/imgs/ubuntu_hostname.png"/>
Ensure that Krb5 is installed: 'sudo apt-get install -y krb5-user sssd-krb5'
<img src="/imgs/krb5.png"/>
Now we need to disable our local DNS and configure Ubuntu to use the Domain Controller by editing the /etc/resolv.conf file:
<img src="/imgs/disable_dns.png"/>
Now we can confirm that we are able to reach the domain by using: realm discover DOMAIN
<img src="/imgs/realm_discover.png"/>
Now we can join the domain and confirm that we have joined using the following commands: 'sudo realm join -U "DOMAIN+ADMIN" "DOMAIN"'
& 'realm list'
<img src="/imgs/realm_join.png"/>
Next we need to reconfigure the mkhomedir file in /usr/share/pam-configs so that when AD users login they get their own individual home directory:
<img src="/imgs/change_pam.png"/>
Then we can restart pam with 'sudo pam-auth-update' and we'll be greeted with the window below ensure you select [*] Create home directory on login:
<img src="/imgs/pam-auth-update.png"/>
Now we'll restart the System Security Services Daemon or sssd and confirm we can view users within AD:
<img src="/imgs/sssd_restart.png"/>
Next, we need to allow domain users to ssh into this system. We'll do that with: 'sudo realm permit --all'. We can also configure sudo for domain admins by creating a new suders file:
<img src="/imgs/allow_ssh.png"/>
Finally we can test ssh by logging in as a domain user onto the localhost
<img src="/imgs/testing_ssh.png"/>
</p>
<b>And We're All Set</b>
  
<br />
<h2>Languages and Utilities Used</h2>

- <b>Bash</b> 
- <b>Raspberry Pi</b>

<h2>Environments Used </h2>

- <b>Ubuntu Server</b>


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
