---
layout: post
title:  "Running Next Cloud on Raspberry PI"
date:   2020-01-04 00:00:00 +0000
categories: Nextcloud
tags:  Nextcloud raspberryPI
---

This Post on [http://unixetc.co.uk](http://unixetc.co.uk/2016/11/20/simple-nextcloud-installation-on-raspberry-pi/) inspired me to set up my own NextCloud server on a raspberry pi. If you want a dead simple install I would follow that guide. But I wanted to go a bit farther and see what kind of performance I could get out of NextCloud with a couple of PIs.

## Hardware

For this I will be using [2 Raspberry Pi 3s](https://www.amazon.com/gp/product/B07BDR5PDW/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1) that I already had on hand. If you where going to buy I would go for the newest version available. The Pi4 is interesting with up to 4GB of ram and USB3 for external storage.

For Data Storage I will be using a 128GB USB Drive. But I plan on replacing this soon and would not recommend this for long term.

For cases I have been using this [Stackable Case](https://www.amazon.com/gp/product/B07BGYGLZG/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1) and it comes with heatsinks as well.

For a Power supply this one from [Anker](https://www.amazon.com/gp/product/B00VH8ZW02/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1) works like a charm.

## Pi Setup

For this I am using Rasbian Buster. Install this as you would normally. But a couple of notes

* Ensure you assign a Static IP or a DHCP reservation.
* Change the password as you will want to expose this to the internet.
* In ```sudo raspi-config```
  * Advanced Options -> Expand Filesystem
  * Advanced Options -> Memory Split -> 16

## External Drive

The actual Files for NextCloud should be stored here. Most External Drives come configured with FAT32, But we will want to reformat the drive to ext4 (ZFS could also be an option)

fdisk is a easy way to find the drive.

``` sh
pi@p2:~ $ sudo fdisk  -l
Disk /dev/sdb: 114.6 GiB, 123060879360 bytes, 240353280 sectors
Disk model: Ultra
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x00000000

Device     Boot Start       End   Sectors   Size Id Type
/dev/sdb1          32 240353279 240353248 114.6G  c W95 FAT32 (LBA)

```

So my drive is /dev/sdb.

For this I wanted to repartition the drive to gpt and wipe it.

``` sh
pi@p2:/mnt $ sudo parted /dev/sdb
GNU Parted 3.2
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel gpt
Warning: The existing disk label on /dev/sdb will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? yes
(parted) mkpart primary ext4 1MiB 100%
(parted) print
Model: SanDisk Ultra (scsi)
Disk /dev/sdb: 123GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End    Size   File system  Name     Flags
 1      1049kB  123GB  123GB  ext4         primary

(parted) quit
Information: You may need to update /etc/fstab.

pi@p2:/mnt $
```

Format Drive to ext4 or what ever you prefer

```sudo mkfs.ext4 /dev/sda1```

Mount the drive (This is mostly to test)

``` sh
sudo mkdir -p /mnt/nextcloud/
sudo mount /dev/sda1 /mnt/nextcloud/ -t ext4
```

Edit /etc/fstab so that it will auto mount on boot
```/dev/sda1 /mnt/nextcloud/ ext4 defaults 0 0```

Reboot your Pi and check that it will boot and the drive is mounted. If you messed up your fstab the PI will not boot. Having a Monitor and Keyboard around to connect to the PI would be very handy at this point.

## Installing the Web Server

Installing apache2

```sudo apt-get install apache2```

Install php and some modules we will need

``` sh
sudo apt-get -y install php php-gd sqlite php-curl php-zip php-xml php-mbstring libapache2-mod-php python-mysqldb php-mysql php-imagick php-intl
```

Restart apache2 to enable everything.

```sudo systemctl restart apache2```

## Marina DB

You can run the Database on the same server that you are hosting the web server on. But for this we will be using a Second Rasberry Pi.
I used Marina DB witch is a fork of Mysql and can be used interchangeably and in some places we use Mysql tools.

Install Marina DB
```sudo apt-get install -y mariadb-server python-mysqldb```

Next we will want to run the mysql_secure_installation to set up the server.
```sudo /usr/bin/mysql_secure_installation```

Next we need to run some SQL Commands to create the Database and configure it.
To jump into it locally use ```sudo mysql -u root```

Create the Database
```create database nextcloud;```

Create the user
```create user ncuser;```

Set the password, Make sure this is a long random password and you won't need to remember this.
```set password for ncuser = password("BAD_PASSWORD");```

Lastly we will need to allow this user to login remotely, edit this to your local subnet
```grant all PRIVILEGES on nextcloud.* to ncuser@'192.168.10.%' identified by 'BAD_PASSWORD';```

## NextCloud

Back on our first PI get the Latest copy of NextCloud

```wget https://download.nextcloud.com/server/releases/latest.zip```

Unzip and Move files to the Web Server Directory

``` sh
unzip -q latest.zip
sudo cp -R nextcloud/* /var/www/html/
sudo mv nextcloud/.htaccess /var/www/html/.htaccess
sudo mv nextcloud/.user.ini /var/www/html/
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
```

Create the Data Dir on our External Drive

``` sh
sudo mkdir -p /mnt/nextcloud/data
sudo chown www-data:www-data /mnt/nextcloud/data
sudo chmod 750 /mnt/nextcloud/data
```

## Set up DNS

You will now want to configure your DNS server to point to your NextCloud install.
I would recommend setting up Split horizon so that when you are at home you will go directly to the WebServer Vs the public IP. You can also configure this so that there is no public access.

For testing you can also edit the **hosts** file on your system.

## Enabling SSL/TLS

You can optionally get a Certificate from Lets Encrypt for this.

Install certbot
```sudo apt install certbot python-certbot-apache```


Get Certificate, This will require that your Pi is internet accessible and you have public DNS set up.
```sudo certbot --apache -d nextcloud.mydomain.com```

If you will not have your Pi publicly accessable you can look into the different [Challenge Types](https://letsencrypt.org/docs/challenge-types/)

## Configure NextCloud

Navigate to your new host

https://nextcloud.mydomain.com

Use the Database information and data dir that you created before.

## Post install tunning

At this point you should be able to log in and start using NextCloud. Below are a couple of Performance tweaks that I did.

### Increasing PHP Memory Limit

Edit /etc/php/7.3/apache2/php.ini to change memory_limit
```memory_limit = 512M```

Restart apache2 to enable.
```sudo systemctl restart apache2```

### Convert Columns to big int

```sudo -u www-data /var/www/html/occ db:convert-filecache-bigint```

### Set up System Cron

```sudo crontab -u www-data -e```

### Enable php-apcu

```sudo apt install php-apcu```

Edit /var/www/html/config/config.php and add ***'memcache.local' => '\OC\Memcache\APCu',***

Restart Web Server

```sudo systemctl restart apache2```

More Info - https://docs.nextcloud.com/server/17/admin_manual/configuration_server/caching_configuration.html

## Conclusion

I really Like Nextcloud, it has lots of features that tax a Raspberry Pi but the FileSync works great and with some more tweaking I should be able to speed it up. Look for more posts on the next things that I try.
