---


---

<h1 id="m300---dokumentation">M300 - LB1 Dokumentation</h1>
<p>Im Modul 300 wurde uns die Arbeit mit Virtuellen Maschienen näher gebracht.</p>
<h2 id="auftrag">Auftrag</h2>
<p>Unser Auftrag war es, verschiedene Services via Vagrant und GitHub zu realisieren. Mit Vagrant kann man mit einem Vagrant-File eine VM erstellen ohne irgendwelche konfigurationen zu machen. Alles befindet sich in diesem File.</p>
<h2 id="unser-vorgehen">Unser Vorgehen</h2>
<p>Wir arbeiteten nach dem IPERKA system. Wir schauten uns den Auftrag genau an und informierten uns. Anschliessen planten wir unser Vorgehen. Die Vorgehensweise wurde vom Auftragsdokument übernommen, da dort alles genau beschrieben wurde. Wir arbeiteten somit das Skript durch. Wir hatten Startprobleme, da es bei Roman nicht geklappt hat Vagrant aufzusetzen. Wir lösten dieses Problem am zweiten Modultag, indem wir alles nochmals neu installierten. Nachdem folgte die Arbeit am Skript. Leider verstanden wir nicht ganz alles, was uns zu einem unseren Mitstiften führte, mit welchem wir ein Vagrant-File erstellten.</p>
<h2 id="github">GITHUB</h2>
<p>Wir haben uns Konten bei GITHUB erstellt. GITHUB bietet die Möglichkeit Dateien auf einfachem Wege hochzuladen, leicht bearbeitbar und offen zugänglich zu machen.</p>
<p>Mit Github ist es möglich den Kontakt zu anderen Entwickler auf der ganzen welt zu aufzubauen. Genau auf diesem Wege werden wir auch unsere fertigen Vagrantfiles mit unserem Lehrer kommunizieren.</p>
<blockquote>
<p><em>“GitHub brings together the world’s largest community of developers to discover, share, and build better software.”</em></p>
</blockquote>
<h2 id="produktive-nutzung">Produktive Nutzung</h2>
<p>Unsere Umgebung ist darauf aufgebaut, dass man mit wenigen Commands eine ganze VM Umgebung aufsetzen kann, die sich automatisch installiert und Konfiguriert.</p>
<p>Bsp. kann ein Informatiker, der mehrere VMs pro Tag aufsetzten muss sich sehr viel Arbeit, Zeit und Nerven sparen.</p>
<p>So kann er mit Git Bash in ein Verzeichnis gehen, wo sich ein Vagrant-File befindet. In diesem Verzeichnis kann er ganz einfach den command “vagrant up” ausführen und das Vagrant-File wird ausgeführt.</p>
<p>In diesem Vagrant-File ist definiert von wo er sich das OS grabben soll, welche konfigurationen er vornehemen soll und wie ist installiert wird.</p>
<h2 id="eigener-service">Eigener Service</h2>
<p>Wir haben bei dem Auftrag des eigenen Services zuerst darauf geschaut, dass wir das Vagrantfile überhaupt verstehen. Durch logisches überlegen, konnten wir anpassungen in diesem File vornehmen.<br>
Sie sehen gleich ein Vagrant-File bei dem Ubuntu mit einer Firewall Reproxy installiert wird. die Ports haben wir so abgeändert, dass jegliche kommunikation von der IP 10.0.2.2 zugelassen wird.<br>
Ausserdem das Protokoll TCP über den Port 80 in jedem Fall zugelassen.<br>
Um sicherzugehen, dass diese Commands auch ausgeführt werden, führen wir den Command <em>Force</em> ein.</p>
<pre><code>Vagrant.configure(2) do |config|   config.vm.box = "ubuntu/trusty64"   config.vm.network "private_network", ip:"192.168.10.1"   config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true   config.vm.synced_folder ".", "/var/www/html"  config.vm.provider "virtualbox" do |vb|   vb.memory = "512"  end   config.vm.provision "shell", inline: &lt;&lt;-SHELL
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo apt-get install ufw -y
    sudo ufw allow from 10.0.2.2 to any port 22
    sudo ufw allow 80/tcp
    sudo ufw --force enable
    sudo apt-get install libapache2-mod-proxy-html -y
    sudo apt-get install libxml2-dev -y
    a2enmod proxy
    a2enmod proxy_html
    a2enmod proxy_http/41
    sed -i '$aServerName localhost' /etc/apache2/apache2.conf
    service apache2 restart
    cd /etc/apache2/sites-enabled
    wget https://pastebin.com/raw/GbjFC2ii
    cp GbjFC2ii 001-reverseproxy.conf   SHELL end
</code></pre>
<h2 id="testing">Testing</h2>
<p>Um das Testing durchzuführen haben wir verschiedene Testcases aufgeschrieben und nach diesen Fällen die Tests druchgeführt.<br>
Dokumentiert wurde ebenfalls das erwartete und das tatsächliche Ergebnis festgehalten.</p>
<p>| 	<strong>Test</strong>	 | <em>Erwartete Ausg.</em> | Tatächliche Ausg.</p>
<p>| <strong>Erstellung VM</strong> | <em>Nach ausführen des Vagrant files, wird die VM erstellt</em>. | Nach ausführen des Vagrant files, wird die VM erstellt.<br>
<img src="https://perrone.myqnapcloud.com:450/share.cgi/187_Erstellun.PNG?ssid=02YbC2K&amp;fid=02YbC2K&amp;path=/&amp;filename=187_Erstellun.PNG&amp;openfolder=normal&amp;ep=" alt=""></p>
<p>| <strong>SSH Verbindung</strong> | <em>Die Verbindung zu dieser VM ist mit SSH möglich.</em> |  Die Verbindung zu dieser VM ist mit SSH möglich.<br>
<img src="https://perrone.myqnapcloud.com:450/share.cgi/187_ssh.PNG?ssid=02YbC2K&amp;fid=02YbC2K&amp;path=/&amp;filename=187_ssh.PNG&amp;openfolder=normal&amp;ep=" alt="enter image description here"></p>
<p>|  <strong>Firewall</strong>  | <em>Die Firewall ist gestartet</em>. | Die Firewall ist gestartet.<br>
<img src="https://perrone.myqnapcloud.com:450/share.cgi/187_test%20firewall.PNG?ssid=02YbC2K&amp;fid=02YbC2K&amp;path=/&amp;filename=187_test%20firewall.PNG&amp;openfolder=normal&amp;ep=" alt=""></p>
<p>|  <strong>Betrieb</strong>  |  <em>Die Website ist ansprechbar.</em>  |  Die Website ist ansprechbar.</p>
<p><img src="https://perrone.myqnapcloud.com:450/share.cgi/Capture.PNG?ssid=02YbC2K&amp;fid=02YbC2K&amp;path=%2F&amp;filename=Capture.PNG&amp;openfolder=normal&amp;ep=" alt="enter image description here"></p>
<p>|  <strong>Apache</strong>  |  <em>Apache ist als Prozess sichtbar.</em>  | Apache ist als Prozess sichtbar.<br>
<img src="https://perrone.myqnapcloud.com:450/share.cgi/187_Apache.PNG?ssid=02YbC2K&amp;fid=02YbC2K&amp;path=/&amp;filename=187_Apache.PNG&amp;openfolder=normal&amp;ep=" alt=""></p>
<p><img src="https://ssl.gstatic.com/ui/v1/icons/mail/images/cleardot.gif" alt=""></p>

