# OpenVPN-Monitor


## Summary

OpenVPN-Monitor is a simple python program to generate html that displays the
status of an OpenVPN server, including all current connections. It uses the
OpenVPN management console. It typically runs on the same host as the OpenVPN
server, however it does not necessarily need to.

[![](https://raw.githubusercontent.com/furlongm/openvpn-monitor/gh-pages/screenshots/openvpn-monitor.png)](https://raw.githubusercontent.com/furlongm/openvpn-monitor/gh-pages/screenshots/openvpn-monitor.png)


## Source

The current source code is available on github:

https://github.com/furlongm/openvpn-monitor


## Quick Install on Debian 8 using apache

### Install dependencies

```shell
apt-get install python-geoip python-ipaddr python-humanize apache2 git
```

### Checkout OpenVPN-Monitor

```shell
cd /var/www/html
git clone https://github.com/furlongm/openvpn-monitor.git
```

### Configure OpenVPN

Add the following line to your OpenVPN server configuration to run the
management console on 127.0.0.1 port 5555:


```
   management 127.0.0.1 5555
```

Refer to the OpenVPN documentation for further information on how to secure
access to the management interface.

### Configure Apache

Choose one of the following two methods to serve under Apache:

#### Method 1: configure Apache with mod_wsgi

```shell
apt-get install libapache2-mod-wsgi
a2enmod wsgi
```

Add the following to /etc/apache2/sites-enabled/000-default.conf

```
    WSGIDaemonProcess openvpn_monitor processes=1 threads=1
    WSGIScriptAlias /openvpn /var/www/html/openvpn-monitor/app.wsgi
    WSGIProcessGroup openvpn_monitor

    <Directory "/var/www/html/openvpn-monitor">
        <Files app.wsgi>
            Require all granted
        </Files>
    </Directory>
```

#### Method 2: configure Apache with mod_python

```shell
apt-get install libapache2-mod-python
a2enmod python cgid
```

Add the following to /etc/apache2/sites-enabled/000-default.conf

```
    <Directory "/var/www/html/openvpn-monitor">
        Options +ExecCGI
        AddHandler cgi-script .py
        DirectoryIndex openvpn-monitor.py
    </Directory>
```

Restart apache:

```shell
/etc/init.d/apache2 restart
```

### Download the GeoLite City Database

```shell
cd /usr/share/GeoIP/
wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
gunzip GeoLiteCity.dat.gz
mv GeoLiteCity.dat GeoIPCity.dat
```

### Configure OpenVPN-Monitor

The example configuration file `/var/www/html/openvpn.cfg` should give some
indication of how to set site name, add a logo, etc. You can also set a default
location (latitude and longitude) for the embedded maps. If not set, the
default location is Melbourne, Australia.

Edit `/var/www/html/openvpn.cfg` to match your site. You should now be able to
navigate to `http://myipaddress/openvpn-monitor`

### Debugging

OpenVPN-Monitor can be run from the command line in order to test if the html
generates correctly:

```shell
cd /var/www/html/openvpn-monitor
python openvpn-monitor.py
```

## License

OpenVPN-Monitor is licensed under the GPLv3, a copy of which can be found in
the COPYING file.


## Acknowledgements

Flags are created by Matthias Slovig (flags@slovig.de) and are licensed under
Creative Commons License Deed Attribution-ShareAlike 3.0 Unported
(CC BY-SA 3.0). See http://flags.blogpotato.de/ for more details.
