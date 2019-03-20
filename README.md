# M300---Service

Vagrantfile
-------------
Zuerst habe ich eine Multi VM Umgebung aufgebaut. Ein Beispiel File und der Code befindet sich <a href="https://github.com/mc-b/devops/tree/master/vagrant/mmdb">Hier</a>.

Für alle VMs wird die Ubuntu Box verwendet und hier noch den <a href="https://app.vagrantup.com/ubuntu/boxes/xenial64">Link</a>. 
Bei meinen Fall habe ich drei VMs erstellt. 


DHCP-Server
-------------
Die DHCP VM hat folgende Spezifikationen (Die Änderungen kann man im Vagrant-File vornehmen):
* IP: 192.168.6.5
* Hostname: dhcp
* RAM: 1024 MB
* VM Box: Ubuntu

```
config.vm.define "dhcp" do |dhcp|
  dhcp.vm.box = "ubuntu/xenial64"
  dhcp.vm.hostname = "dhcp"
  dhcp.vm.network "private_network", ip:"192.168.6.5" 
	dhcp.vm.provider "virtualbox" do |vb|
	  vb.memory = "1024"  
end  
```
Der erstes Schritt ist die Paketverzeichnis aktualisiert wird. 

```
sudo apt-get update
```
Im nächsten Schritt wird eine Gruppe mit inklusvie Benutzer mit Passwort erstellt. Es sieht so aus: 
```
#Gruppe Myadmin erstellen
sudo groupadd myadmin
#User erstellen
sudo useradd admin -g myadmin -m -s /bin/bash
sudo useradd test -g myadmin -m -s /bin/bash
#Password festlegen
sudo chpasswd <<<admin:admin
sudo chpasswd <<<test:test
```
Bei nächsten Schritt wird der DHCP Server installiert. Das Paket lautet: ISC-DHCP-SERVER.
```
sudo apt-get -y install isc-dhcp-server
```
Nach der Installation von DHCp-Server muss man die Konfiguration anpassen. Das Konfigurationfile vom DHCP Server befindet sich im Pfad /etc/dhcp/dhcpd.conf. Bei dem Konfigurationfile wird folgendes geändert:
* Domainname
* DNS
* DHCP Scope

Der Domainname lautet Standardmässig example.org und will neu labor.local umändern. 
```
#Domainanme konfigurieren
sudo sed -i 's/example.org/labor.local/g' /etc/dhcp/dhcpd.conf
```
Der DNS wird nun auf 8.8.8.8 (Google DNS) konfiguriert.
```
#DNS konfigurieren
sudo sed -i 's/ns2.labor.local/8.8.8.8/g' /etc/dhcp/dhcpd.conf
```
Im Bereich beim Scope hat folgende Parameter:
* Subnet --> 192.168.6.0/24
* Range --> 192.168.6.100 - 130
* Gateway --> 192.168.6.1
```
#DHCP Autorisierung aktiviert
sudo sed -i 's/#authoritative/authoritative/g' /etc/dhcp/dhcpd.conf
#DHCP Subnetz & Maske konfigurieren
sudo sed -i '$asubnet 192.168.6.0 netmask 255.255.255.0 {' /etc/dhcp/dhcpd.conf
#DHCP Range konfigurieren
sudo sed -i '$arange 192.168.6.100 192.168.6.130;' /etc/dhcp/dhcpd.conf
#DHCP Gateway konfigurieren
sudo sed -i '$aoption routers 192.168.6.1;' /etc/dhcp/dhcpd.conf
sudo sed -i '$a}' /etc/dhcp/dhcpd.conf
```
Nach der Änderung des Konfigurationsfile wird der DHCP Service neu gestartet.
```
#DHCP Server neustarten
sudo service isc-dhcp-server restart
```
Die Tastaturlayout muss man noch auf Deutsch Schweiz anpassen.
```
#Tastaturlayout anpassen
sudo sed -i 's/XKBLAYOUT="us"/XKBLAYOUT="ch"/g' /etc/default/locale
```
Am Ende wird noch eine lokale Firewall installiert und anschliessend auch konfiguriert. Dabei öffnen man den Port 22 um via SSH darauf zuzugreifen. INFO-INPUT SSH Port 22 | TELNET Port 23
```
#Local Firewall installieren
sudo apt-get -y install ufw gufw 
sudo ufw allow from 10.0.2.2 to any port 22
sudo ufw --force enable    
```
Nun ist die DHCP-Part abgeschlossen. Man kann jetzt Clients VM erstellen und mit dem DHCP-Server innerhalb IP-Range IP-Adresse bekommen.

FTP-Server
----------
Die FTP VM hat folgende Spezifikationen (Die Änderungen kann man im Vagrant-File vornehmen):
* IP: 192.168.6.6
* Hostname: ftp
* RAM: 1024 MB
* VM Box: Ubuntu

```
config.vm.define "ftp" do |ftp|
  ftp.vm.box = "ubuntu/xenial64"
  ftp.vm.hostname = "ftp"
  ftp.vm.network "private_network", ip: "192.168.6.6"
  ftp.vm.network "forwarded_port", guest:3306, host:3306, auto_correct: true
  ftp.vm.provider "virtualbox" do |vb|
	 vb.memory = "1024"  
end
```
Der erstes Schritt ist erneut die Paketverzeichnis zu akualisieren. 

```
sudo apt-get update
```
Bei nächsten Schritt wird der FTP Server installiert. Das Paket lautet: pure-ftpd
```
#FTP Server installieren
sudo apt-get -y install pure-ftpd-common pure-ftpd
```
Nach der Installation kann ich die FTP-Server konfigurieren. Bei meinem Fall sieht es so aus: 
```
sudo groupadd ftpgroup
sudo useradd -g ftpgroup -d /dev/null -s /etc ftpuser
sudo pure-pw useradd samjoy -u ftpuser -g ftpgroup -d /home/pubftp/samjoy -N 10
```
Nach der Änderung wird der FTP Service neu gestartet.
```
#FTP Server neustarten
sudo service pure-ftpd-common pure-ftpd restart
#sudo /home/pubftp/samjoy restart
```
Die Tastaturlayout muss man noch auf Deutsch Schweiz anpassen.
```
#Tastaturlayout anpassen
sudo sed -i 's/XKBLAYOUT="us"/XKBLAYOUT="ch"/g' /etc/default/locale
```
Am Ende wird noch eine lokale Firewall installiert und anschliessend auch konfiguriert. Dabei öffnen man den Port 22 um via SSH darauf zuzugreifen. INFO-INPUT SSH Port 22 | TELNET Port 23
```
#Local Firewall installieren
sudo apt-get -y install ufw gufw 
sudo ufw allow from 10.0.2.2 to any port 22
sudo ufw --force enable    
```
Nun ist die FTP-Part abgeschlossen.

Apache-Server
----------
Die Apache VM hat folgende Spezifikationen (Die Änderungen kann man im Vagrant-File vornehmen):
* IP: 192.168.6.7
* Hostname: apache
* RAM: 1024 MB
* VM Box: Ubuntu

```
config.vm.define "apache" do |apache|
  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = "apache"
  config.vm.network "private_network",ip:"192.168.6.7",netmask:"255.255.255.0",default_gateway:"192.168.6.1"
  config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
  config.vm.synced_folder ".", "/var/www/html"
  config.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
end
```
Der erstes Schritt ist ebenfalls erneut die Paketverzeichnis zu aktualisieren. 

```
sudo apt-get update
```
Im nächsten Schritt wird eine Gruppe mit inklusvie Benutzer mit Passwort erstellt. Es sieht so aus: 
```
#Gruppe Myadmin erstellen
sudo groupadd myadmin
#User erstellen
sudo useradd admin -g myadmin -m -s /bin/bash
sudo useradd test -g myadmin -m -s /bin/bash
#Password festlegen
sudo chpasswd <<<admin:admin
sudo chpasswd <<<test:test
```
Bei nächsten Schritt wird der Apache Server installiert und die Service wird neugestartet. Das Paket lautet: apche2
```
#Installation Apache2
sudo apt-get -y install apache2
#Apache2 Service neustarten
sudo service apache2 restart
```
Die Tastaturlayout muss man noch auf Deutsch Schweiz anpassen.
```
#Tastaturlayout anpassen
sudo sed -i 's/XKBLAYOUT="us"/XKBLAYOUT="ch"/g' /etc/default/locale
```
Am Ende wird noch eine lokale Firewall installiert und anschliessend auch konfiguriert. Dabei öffnen man den Port 22 um via SSH darauf zuzugreifen. INFO-INPUT SSH Port 22 | TELNET Port 23
```
#Local Firewall installieren
sudo apt-get -y install ufw gufw 
sudo ufw allow from 10.0.2.2 to any port 22
sudo ufw --force enable    
```
Nun ist auch die Apache-Part abgeschlossen.

Aufsetzten von VMs via Vagrantfile
----------
Mit folgendem Befehl kann die VM aufgesetzt werden.
```
vagrant up
```

Alle Informationen, Angaben und Textbeiträge innerhalb sind ohne Gewähr auf Vollständigkeit und Richtigkeit.
