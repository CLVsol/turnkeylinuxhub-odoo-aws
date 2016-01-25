turnkeylinuxhub-odoo-aws
========================

This project will help you install `Odoo 8.0 <https://www.odoo.com/>`_  appliance, using the Amazon Web Services (AWS) EC2 infrastructure.

This project is based on `Turnkey Odoo Appliance <https://github.com/turnkeylinux-apps/odoo>`_ project.

#. Create a new Key Pair:

	* Key pair name: **tkl-odoo-aws**
	* Private Key File: **tkl-odoo-aws.pem**

#. Create, via TrunKey Hub, an Amazon EC2 instance:

		- TurnKey Appliance: **Odoo**
		- Hostname: **[tkl-odoo-aws]**
		- Region: **[Sao Paulo (South America)]**
		- Size: **[T2.Micro ($0.027/hour)]**
		- SSH key-pair: **[tkl-odoo-aws]**
		- Root file system size (GB): **[10]**

	Related information:

		- Security Groups: **[turnkey-odoo-xxxx]**
		- Private Key File: **[tkl-odoo-aws.pem]**

	Security Group: [turnkey-odoo-xxxx] (Inbound)::

		Port (Service)   Source
		-------------------------------------
		N/A(PING)        0.0.0.0/0
		22(SSH)          0.0.0.0/0
		80(HTTP)         0.0.0.0/0
		443(HTTPS)       0.0.0.0/0
		12320(Web Shell) 0.0.0.0/0  (disable)
		12321(Webmin)    0.0.0.0/0  (disable)
		12322(Adminer)   0.0.0.0/0  (disable)

#. Upgrade the software:

	::

		apt-get update
		apt-get -y upgrade

#. `Disable Password-based Login <http://aws.amazon.com/articles/1233?_encoding=UTF8&jiveRedirect=1>`_:

	Log in to your instance as root and edit the ssh daemon configuration file "**/etc/ssh/sshd_config**"

	Find the line::

		PasswordAuthentication yes

	and change it to::

		PasswordAuthentication no

	Save the file and restart sshd::

		/etc/init.d/ssh restart

	**You will now only be able to log in with an ssh key.**

#. Update host name, executing the following commands:

	::

		HOSTNAME=tkl-odoo-aws
		echo "$HOSTNAME" > /etc/hostname
		sed -i "s|127.0.1.1 \(.*\)|127.0.1.1 $HOSTNAME|" /etc/hosts
		/etc/init.d/hostname.sh start

#. Copy file "/etc/odoo/openerp-server.conf" into "/etc/odoo/openerp-server-man.conf". Edit the file "/etc/odoo/openerp-server-man.conf":

	::

			#logfile = False
			logfile = /var/log/odoo/openerp-server.log

	::

			logfile = False
			#logfile = /var/log/odoo/openerp-server.log


#. (Optional) Reboot the instance "tkl-odoo-aws".

#. To stop and start the Odoo server, use the following commands (as root):

	::

		/etc/init.d/openerp-server stop

		/etc/init.d/openerp-server start

	::

		cd /opt/openerp/odoo
		su openerp
		./openerp-server -c /etc/odoo/openerp-server-man.conf
