# docker-island
Fastest way to setup a Self-Hosted private cloud envrionment

This documentation is a simple written summary to assist newcomers using Treafik for Self Hosted services requiring SSL, Port mapping and internal routing of docker services. This guide is a Reference Point for starting a docker self hosted network within an hour or two (hopefully). Keep in mind the guide is always work in progress to help you speed through the process.

 (Recommended but no necessary)  installing an open source fast forwarding google chrome extension to go through YouTube videos at 2x or 3x the speed. https://github.com/igrigorik/videospeed 

0. To start it is recommended but no necessary using ProxMox for creating Ubuntu environments. Instructions: https://www.youtube.com/watch?v=7OVaWaqO2aU
   Working as 2022 Q3 ^
   It is best to use a hypervisor ( ProxMox ) for creating virtual machines in order to run different Ubuntu environments such as setting up a reverse proxy's and for other additional benefits such as taking snapshots to revert back changes or testing different Ubuntu Setups which will save you a lot of time.
  (no need to view but this video but it provides you a list of benefits for using virtual machines:)
   https://www.youtube.com/watch?v=SVQmzaSabEQ
  
   (skip for now, needs more testing) In addition from a security a standpoint it is recommended but not necessary creating a Virtual LAN to avoid any attacks in your real LAN. https://www.youtube.com/watch?v=qTbeHpdHcqs
   ( if using a laptop or pc as your main setup, you can also purchase a USB to Ethernet Adapter for ProxMox to recognize it as an additional network which you can assign a VLAN, then you can start creating virtual machines on that VLAN )
   
1. Ubuntu+Docker+Docker Compose, Fresh install
   Docker Installation and: https://www.youtube.com/watch?v=EL1Ex04iUcA ( watch up until 14:07 and be finished )
 
## 2. Before starting it is also recommended to have a DNS program to manage your SubDomain.MyDomains.com entries ye2
<details>
  <summary>Setup DNS for your Envrionment 2</summary>
   1. for example mapping your local 192.168.10.5:9000 translated into portainer.mydomain.com therefore making it easier for you to Name your docker services as sub domains for easier management. (in addition Traefik will help routing to the correct services) Having a DNS program will allow you to edit and manage your DNS entries using AdGuard or Pi-Hole DNS. Make sure to reload browser after adding a DNS entry if Installing Adguard from the SNAP store. 
   * If you install Adguard  ( Adgurd->Filters->DNS Rewrites)
</details>
--------------------

Also CloudFlare allows you to add DNS entries from their website but it is not recommended as it exposes another attack vulnerability to your network. A better solution is to install a Local DNS management software on your network. so you can lessen attacks and keep your internal IP and ports routing Locally to your system and services. ( you can install it on the same system or an a external ProxMox virtual machine or on a Raspberry Pie to point it to your Treafik setup. The easy way is to install AdGuard from the Ubuntu Software App store (SNAP) you simply search and download AdGuard. Or visit Pi-Hole website and run the automated script that will guide you through the process.
Pi-Hole One step automated install: https://github.com/pi-hole/pi-hole/#one-step-automated-install the gotcha's are changing your DNS on your resolv.conf to your local ip
You can watch this video up till 4:06 and be done: https://youtu.be/dH3DdLy574M

3. To start using Treafik, it is Important to own a domain name as docker services will require SSL. You can purchase a cheap $6 dollar a year domain name preferably from CloudFlare or any other Service Provider. If purchased elsewhere make sure to point your NameServers to CloudFlare. ( 2022 Q3: cloudflare domain prices: .uk $4.76, .us $6.50, .com $8.57) or you can try free domains from https://www.freenom.com ( when searching for a domain it will trick you into thinking it has been taken but it's not true, just enter the ending of the domain you want, example: myfreedomain.tk to get it when searching for it )
  To buy a domain name from cloudflare you can find it via ( CloudFlare account -> Top Right, Add Site button ->Register a new domain with Cloudflare )
   https://www.cloudflare.com/products/registrar/
   It is necessary to create a CloudFlare account for using OnRamp, The CloudFlare account will be necessary as it will generate an API token which will be used to connect your free SSL to your Internal Treafik network. 
Reference video only ( stop at 5:20 ) to move on with this guide: https://www.youtube.com/watch?v=b83S_N1kkJM
What I did was add a "A" Record to my internal IP and a CNAME pointing to my Treafik subdomain example treafik.mydomain.com ( you can name it whatever etc.mydomain.com)
In CloudFlare make sure you create the api token before you proceed! 

4. Your Personal Router, It is also important to do Port Mapping on your router in order to point the incoming SSL from CloudFlare connection to your internal Treafik computer.. the Ports from CloudFlare to your Ubuntu machine running Treafik example 192.168.10.etc. You can YouTube your router model on how to do port forwarding, it might be under WAN ->Virtual Server / Port Forwarding.
   Enabling it and by entering your HTTP server External Port 80 to Internal Port 80 and adding another Entry for HTTP Server External Port 443 to Internal 443 TCP protocol.
   I managed to get it working before using Tim's Guide SSL on everything without port forwarding but it might not work for other services. There is also another guide made by DB Tech to avoid using Port Forwarding, just by using CloudFlare Zero Trust Tunnels ( not tested yet, but I heard people were getting banned in the comments due to TOS violations from using streaming services like kodi ) https://www.youtube.com/watch?v=m-RYTu-Qq3A or https://youtu.be/RUJy9fjoiy4
   
5. Finally, Installing Treafik using https://github.com/traefikturkey/onramp
   Hopefully this guide will help you to finally get Docker running with Treafik services correctly, and Many Thanks! to contributors from TechnoTim's discord Community for making TreafikTurkey and OnRamp! 
   So to finish off, Make sure You copy and paste the commands individually line by line in your terminal from onramp:
   
sudo apt install git make nano -y

sudo mkdir /apps

sudo chown -R $USER:$USER /apps

cd /apps

git clone https://github.com/traefikturkey/onramp.git onramp

cd onramp

make start-staging

Remove placeholder content including the brackets: < replace > example: 
HOST_NAME=< replace > with:
HOST_NAME=mypcname

If you don't know your hostname, in Ubuntu you can view it by:
right clicking on ubuntu desktop ->Display Settings -> left side, scroll all the way down to About -> Device Name ( you will find your host name) if you would like to change it will require a Restart.

make sure it is in the correct directory ! don't clone it anywhere else. I believe the correct directory is Computer/apps/onramp. You can verify by clicking in Ubuntu Files -> Left Side +Other Locations -> Computer -> Apps -> onramp
That's where the make start-staging script should run.

 Also if you mess up, don't stress out, you can edit the script again.

the make start-staging will run the Makefile script and will create a hidden .env file with your provided HOSTNAME and Token information, so if it's not working, you can always go back and edit the hidden file by running this command:
 cd /apps/onramp
ls -lha
 ( you will see the .env listed )
 sudo nano .env
( to edit the file ^ )

go back and re-run the make start-staging 
if something is not working, go back to check on your CloudFlare to make sure you "A" record is pointing to your home IP and as previously mentioned a CNAME record to your subdomain example: ( treafik.mydomain.com ) 

Once you get it working it the script will get you Staging SSL certificate for testing to make sure your connection went through. If you can confirm it's working, you can do so by click the url link it provided to you for your treafik dashboard ( from running the script in terminal. )

Finally if you can verify you have SSL via your browser then you are good to proceed to the final step which is taking down the staging certificates to get real SSL certificates
( just confirm you are getting SSL on your browser )
Awesome if you did! Last thing to do now, is to take down the staging certificates to get real ones by doing so:

make down-staging
and
make
^ this will get your real SSL certificates.

Congrats now it's time to start spinning up instances of Docker services using the OnRamp commands: 
make list-services

and to install them for example: 
make enable-service portainer
make restart
^ After enabling a service, make sure to run (make restart) in order for the service to show up in your treafik dashboard.

Hopefully this guide can help you to facilitate the process or if you come across problems.
Feel free to provide feedback for anyone reading this guide and if this worked for you, please provide feedback and how long it took you to get up and running Traefik thanks.

Other Collections of Self Hosted Treafik setups to consider:
https://github.com/PAPAMICA/docker-compose-collection
https://github.com/SimonHaas/homeserver
https://github.com/BaptisteBdn/docker-selfhosted-apps
https://github.com/VirtualEstatePlanner/MegaDocker
https://virtualestateplanner.github.io/MegaDocker/
https://github.com/caprover/caprover
