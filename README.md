# unifikursus
0. What you will physically need
 

- Raspberry Pi 3 model B(+) or Pi 4 with a MicroSD card of atleast 4GB

- Ethernet cable to connect it to an existing network

- capability to connect the MicroSD card to a computer via a card reader

 

1. Download the necessary tools and raspbian operating system image
 

1. Raspberry Pi OS Lite (= without desktop interface) => 32-bit only (64-bit does not work)

https://www.raspberrypi.com/software/operating-systems/

Direct: https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2022-01-28/2022-01-28-raspios-bullseye-armhf-lite.zip

 

2. Rufus to install the image on to the MicroSD card.

https://rufus.ie/

Direct: https://github.com/pbatard/rufus/releases/download/v3.17/rufus-3.17.exe

 

3. Advanced IP Scanner to figure out the assigned IP address of your Raspberry Pi.

http://www.advanced-ip-scanner.com

Direct: http://www.advanced-ip-scanner.com/download/Advanced_IP_Scanner_2.5.3850.exe

 

4. If you are not using Windows 10 or 11, you can use PuTTY instead of the command prompt to connect to your Raspberry Pi.

https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

Direct: https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.76-installer.msi

 

5. If you are having issues with the partitions on your MicroSD card, I recommend using MiniTool Partition Wizard. This program makes it very easy to start from scratch by removing every partition on your MicroSD and creating a new Fat32 partition.

https://www.partitionwizard.com/free-partition-manager.html

Direct: https://cdn2.minitool.com/?p=pw&e=pw-free

 

2. Install Raspbian on the MicroSD
Open up Rufus to install Raspbian.

Make sure your MicroSD card is selected
Press SELECT and select the downloaded raspios-xxxx.zip
Tip: you don’t have to extract the .zip

Press START
Accept the warning about removing all of the data on the MicroSD (-> All data on that microSD card will be deleted!)
wait until the process is done.
Windows may ask you to format the Linux partition, do not do this, just cancel the request.
 (click on the image to view it entirely)


.

.

3. Enable SSH to remotely control the Raspberry Pi
Enable SSH before starting the Raspberry Pi by adding a new text file to the main folder of the MicroSD card (boot partition).

Rename this text file to  ssh    and remove the .txt extension.


The MicroSD is ready now, insert it into your raspberry pi, connect an ethernet cable and turn on the Raspberry Pi.

.

Note: It is possible that Windows/Rufus has not assigned a driveletter to the "boot" partition. If you cannot see the drive in file explorer, do a right-mouse click on start, click on disk management and assign a letter to the "boot" partition by right clicking on it and assign a drive letter. You can also use the MiniTool Partition Wizard to do this.

.

.

4. Lookup the IP address
If you’re using Advanced IP Scanner, you can simply press SCAN and wait until the Raspberry Pi shows up.


.

5. Login via SSH
 .

1. Open up the command prompt (search for cmd in start or the dedicated search box)

Tip: You should be able to paste commands by right clicking into the command prompt.

.

2. Login with the SSH command

Note: change the IP address with the IP address of your Raspberry Pi (obtained in step '4. Lookup the IP address')

ssh 10.1.0.5 -l pi
 .

3. As we have not defined an SSH key, it will ask if we trust the host.

Type: yes and press enter.

The authenticity of host '10.1.0.5 (10.1.0.5)' can't be established. 
ECDSA key fingerprint is SHA256:XXX. Are you sure you want to continue connecting (yes/no)?   yes
 .

4. When typing in the password, you will not see characters being displayed, simply type it and enter

The default password is:  raspberry
.

.

____________________________________________________________________________________________________________________________________________

Use the script to save time (or skip this part if you wish to do every step manually)

By running the following line of code, every necessary command will automatically run for you. You choose which version of UniFi to install.

You can view the script by looking on my Github page: https://github.com/SmokingCrop/UniFi/blob/master/install-unifi-pihole-English.sh

.

To install the UniFi controller and Pi Hole, copy/paste/run the following line of code:

wget "https://github.com/SmokingCrop/UniFi/raw/master/install-unifi-pihole-English.sh" -O install-unifi-pihole.sh && chmod +x install-unifi-pihole.sh && ./install-unifi-pihole.sh
.

To only install the UniFi controller, copy/paste/run the following line of code:

wget "https://github.com/SmokingCrop/UniFi/raw/master/install-unifi-pihole-English.sh" -O install-unifi-pihole.sh && chmod +x install-unifi-pihole.sh && ./install-unifi-pihole.sh no-pihole
 ____________________________________________________________________________________________________________________________________________

.

.

6. Change the default password
It's very unsafe to keep the default password, so we'll change it.

Enter the passwd command, it'll ask you to type the current password once and your new password twice.

pi@raspberrypi:~ $ passwd 
Changing password for pi. 
(current) UNIX password: 
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully

.

7. Add the Raspbian Stretch sources.list for MongoDB compatability
As the UniFi controller uses an old MongoDB version, it's necessary to add the old Raspbian Stretch sources.list. Otherwise the UniFi controller will not work with the latest Raspbian OS.

echo 'deb http://archive.raspbian.org/raspbian stretch main contrib non-free rpi' | sudo tee /etc/apt/sources.list.d/raspbian_stretch_for_mongodb.list
.

8. Download and install updates
To make sure you have all the latest updates and to clean up any unused/old ones, execute the following command:

 sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt-get autoclean -y
.

9. Install Java 8
The default available Java version from Oracle is too old to make the 6.X UniFi controller work. Instead of doing multiple extra commands to upgrade this version, we're just going to install OpenJDK. It also requires the 'jsvc' and 'libcommons-daemon-java' packages.

 sudo apt install openjdk-8-jre-headless jsvc libcommons-daemon-java -y
.

 10. Install haveged
In order to fix the slow start-up of the UniFi controller, we have to install haveged. The reason for the slow start-up is the lack of user interaction (no mouse movements) causing issues with the mechanisms for 'harvesting randomness'.

 sudo apt install haveged -y
.

 11. Install MongoDB
The UniFi controller uses MongoDB. You can skip this, but then the installation of the UniFi controller will notice on its first try that it is missing and will install it anyways and then try to install UniFi again. So it's better to just install it already.

 sudo apt install mongodb-server mongodb-clients -y
.

12. Install the UniFi Controller
 

I'll show two ways of installing the UniFi Controller.

Method A: manually installing the Controller by using the download link on the website/update blog.

Easily install any Controller version you want, whenever you want. As soon as the blog comes out.
If you're not using the script, it'll take more commands to update your Controller in the future
(I personally prefer method A)

.

Method B: Setting up the repository so you can install and update with the apt command.

Once it is setup, you can install/update the Controller like any other program with apt.
When a new Controller version is out, it takes like a week before you'll be able to install it.
It's recommended to still go the blog of the update to know if any changes will affect your setup!
.

.

12.1 Install the UniFi Controller with method A

1. Use the link with the wget command to download the Controller installation package.

wget https://dl.ui.com/unifi/7.0.23/unifi_sysvinit_all.deb
.

2. Install UniFi and its dependencies.

sudo dpkg -i unifi_sysvinit_all.deb; sudo apt install -f -y
.

Go to your UniFi Controller via the IP address and port:

E.g: https://10.1.0.5:8443

 

You’ll want to set a reserved (fixed) IP address in the UniFi Controller for the Raspberry Pi.

.

You can go to the (beta) releases page and copy the Debian download link at the bottom of the posts ("UniFi Network Application for Debian/Ubuntu and UniFi Cloud Key") if you want a different version than the one used in this tutorial.

https://community.ui.com/releases?q=Network+Application

https://www.ui.com/download/unifi/


.

.

12.2 Install the UniFi Controller with method B

1. Install apt-transport-https in order to be able to use the 'deb' lines over HTTPS in the /etc/apt/sources.list

sudo apt install apt-transport-https -y
. 

2. Add a new source to the list of sources from which packages can be obtained with 'apt'

echo 'deb https://www.ui.com/downloads/unifi/debian stable ubiquiti' | sudo tee /etc/apt/sources.list.d/100-ubnt-unifi.list
. 

3. Add the GPG keys

sudo wget -O /etc/apt/trusted.gpg.d/unifi-repo.gpg https://dl.ui.com/unifi/unifi-repo.gpg
. 

4. Update the packages after adding the new source and Install the UniFi package

sudo apt update && sudo apt install unifi -y
.

Go to your UniFi Controller via the IP address and port:

E.g: https://10.1.0.5:8443

You’ll want to set a reserved (fixed) IP address in the UniFi Controller for the Raspberry Pi.

.

.

13. Install Pi-hole
Execute the following command and configure Pi-hole

 

curl -sSL https://install.pi-hole.net | bash
> Choose an interface: (*) eth0  (= ethernet cable)  
> Choose your DNS provider: Custom 1.1.1.1, 8.8.8.8 (= fast cloudflare dns & basic Google dns)  or any other option
> Choose your adblocking providers: leave them all enabled  
> IPv4 and/or IPv6: both  
> PiHole wants to set a static IP. Let it do its thing. I guess they haven’t heard of DHCP reservation/central management yet.  
> Install the Web admin interface and the web server
> privacy mode for FTL: 0 Show everything

Change the password used to log into the web admin interface

 

pi@raspberrypi:~ $ pihole -a -p 
Enter New Password (Blank for no password): 
Confirm Password:   
[✓] New password set
 

Go to your Pi Hole web admin interface via the IP address and /admin:

E.g: http://10.1.0.5/admin

 

--I'm only able to write 10 000 characters per post. Check the accepted answer below--
