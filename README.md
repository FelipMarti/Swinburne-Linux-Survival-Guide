



# How to GNU/Linux survive at Swinburne University of Technology

This guide intents to compile all the configuration steps I've been using to survive at [Swinburne](https://www.swinburne.edu.au/) as a GNU/Linux user.

This guide is mostly focused on Debian-based computers (e.g., Ubuntu, Debian, Raspberry Pi OS, etc.) because my research in IoT, Robotics and Vision requires those operating systems. However, those configuration steps can easily be translated to other GNU/Linux distros.

This guide might be useful to staff, HDR students, and some sections to undergraduate students (e.g., no access to staff printers).

- [Eduroam](#eduroam)
- [Evolution](#email-client-evolution-with-office365-and-mfa)
- [Teams](#teams)
- [Staff Printers](#staff-printers)
- [Network Time Protocol (NTP)](#network-time-protocol-ntp)
- [OneDrive](#onedrive)
- [Multi-Factor Authentication (MFA)](#multi-factor-authentication-mfa)



## Eduroam

Security: `WAP & WPA2 Enterprise`

Authentication: `Protected EAP (PEAP)`

Anonymous Identity: Leave this empty

Ca certificate: `(None)`. No CA certificate is required

PEAP version: `Automatic`

Inner authentication: `MSCHAPv2`

Username: `<username>@swin.edu.au`

Password: Your email password

[More info.](https://itwifi.swinburne.edu.au/eduroam/eduroam_guide.pdf)


### Undergraduate Students
Same but be aware that your username is: `<student_ID>@swin.edu.au` and NOT `<student_ID>@student.swin.edu.au`.



## Email Client (Evolution with Office365 and MFA)

Swinburne uses a bunch of Microsoft products such as Office365 that works with Exchange Web Services (EWS) API. Evolution has a plug-in that works pretty well with Exchange Web Services, it will synchronise your email and calendar (but it is not good enough to schedule meetings).

You can install it with the following command:

`sudo apt-get install evolution evolution-ews`

### Configuration

E-mail Address: `<username>@swin.edu.au`

----
Server Type: `Exchange Web Services`

----
Username: `username@swin.edu.au`

Host URL: `https://outlook.office365.com/EWS/Exchange.asmx` don't Fetch URL.

----
Authentication: `OAuth2 (Office365)`

Tenant: `df7f7579-3e9c-4a7e-b844-420280f53859`

Application ID: `22727452-3b5d-4848-ad0c-a4880070afc6`

[More info.](https://wiki.gnome.org/Apps/Evolution/EWS/OAuth2)


### Undergraduate students
It should also work for students if using: `<student_ID>@student.swin.edu.au`


### Alternative (Thunderbird)

At the time of writing, I see the I4T lab also has a guide to configure [Thunderbird.](http://i4t.swin.edu.au/faq/thunderbird.html)

## Teams

Online meetings are mostly done via Microsoft Teams, you can download the GNU/Linux version as a DEB or RPM package from their [website](https://www.microsoft.com/en-au/microsoft-teams/download-ap).

You can install Teams in Debian-based distros with the following command:

`sudo dpkg -i teams_whatever-version.deb`

or alternatively, you can install Teams with the Red Hat Package Manager (RPM) with the following command:

`sudo rpm -ivh teams_whatever-version.rpm`



## Staff Printers

Fuji Xerox FX ApeosPort-V C4475 is the most common (or the only?) staff printer you will find on campus. You need to install the Fuji Xerox printer driver `printer-driver-fujixerox`:

`sudo apt-get install printer-driver-fujixerox`

* Add additional printer
* Enter URI
* Enter device URI: `lpd://username@hap-ps07.ds.swin.edu.au/Staff%20Printer` where username is your staff ID
* Choose Driver: Select printer from database: `FX`
* `FX Printer Driver for Linux`



[More info.](http://caia.swin.edu.au/faq/printing/)


## Network Time Protocol (NTP)

There is nothing more annoying that not having the clock of your system synchronised. The great firewall of Swinburne blocks external NTP servers making things more painful. However, there is a Swinburne NTP server that can be used to synchronise the time of your system on campus: `ntp.swin.edu.au`

Those configuration steps are very helpful if you are dual booting, using OneDrive, a Raspberry Pi, or you have your MFA keys in your computer.

You will need to edit `/etc/systemd/timesyncd.conf` file.

1. Uncomment the NTP line 
2. Add the list of NTP servers you see in FallbackNTP
3. Add the Swinburne NTP server at the end of the NTP list (list of servers is space-separated)

E.g.,

`NTP=ntp.ubuntu.com ntp.swin.edu.au`

If the computer is a desktop that is always going to stay on campus, you can just add the Swinburne NTP server there. E.g.,

`NTP=ntp.swin.edu.au`

[More info.](http://manpages.ubuntu.com/manpages/jammy/man5/timesyncd.conf.5.html)


## OneDrive

[Rclone](https://rclone.org/) is the best software to synchronise any cloud storage service, it works for Google Drive, Amazon S3, Dropbox, OneDrive, and much more.

It is better to install rclone from the [source code](https://github.com/rclone/rclone) or from the official [binaries](https://rclone.org/downloads/) rather than your distro repo. Most likely the binary from the repo will be outdated.

### Configuration
Once installed it, you need to configure the remote:

`rclone config`

`n` New remote

Name: `OneDrive`

Storage: Search for the number of Microsoft OneDrive "onedrive"

`client_id` and `client_secret` can be left empty

Region: `Microsoft Cloud Global`

Advanced config: `No`

Auto config: If installing that in a server (no GUI) you might need to configure it manually. Otherwise auto config: `Yes`.

Login in the web browser and go back to the terminal.

config_type: `OneDrive Personal or Business`

Once configured, you can test if it works listing all the files in your OneDrive with the following command:

`rclone ls OneDrive:`

----

### Usage
To mount OneDrive you will need a destination folder:

`mkdir ~/OneDrive`

and execute the following command to [mount](https://rclone.org/commands/rclone_mount/) your OneDrive:

`rclone --vfs-cache-mode writes mount OneDrive: ~/OneDrive`

You can add a Startup Application that executes the following command to automatically mount your OneDrive:

`sh -c "rclone --vfs-cache-mode writes mount OneDrive: ~/OneDrive"`

If you need to push data from your edge device to the cloud, you can use rclone [copy](https://rclone.org/commands/rclone_copy/). E.g.,:

`rclone copy /home/pi/Data/ OneDrive:Project/Destination/`

### Bonus

Get some folders encrypted in the cloud to protect your privacy.

https://rclone.org/crypt/


## Multi-Factor Authentication (MFA)

You can install your MFA keys in multiple devices (e.g., phone and computer), so you can have a backup in case your phone goes flat, etc. This will also make the login to your accounts from your computer less annoying.

If you are concerned about security you should consider storing your MFA keys in open source software. For example [andOTP](https://github.com/andOTP/andOTP) on your phone, and [OTPClient](https://github.com/paolostivanin/OTPClient) on your computer.

Given that we are going to store Time-based One-time Password (TOTP) keys, it is important that you have your computer [clock synchronised](#network-time-protocol-ntp).



### OTPClient

Hopefully you can install this directly from the repo, because it is annoying all the dependencies if compiling and installing it manually:

* libgcrypt20-dev 
* libpng-dev 
* libzip-dev 
* libjansson-dev 
* libzbar-dev 
* libgtk-3-dev
* [libbaseencode](https://github.com/paolostivanin/libbaseencode)
* [libcotp](https://github.com/paolostivanin/libcotp)

Once installed, you can import your MFA keys from your phone in multiple ways: webcam, QR code, manually, etc.

