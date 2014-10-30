lamps
=================

Combined out-of-the-box LAMP image (PHP+MySQL) from tutum/lamp plus ssh from tutum/ubuntu
and samba sharing of the apps folder


Usage
-----

To create the image, execute the following command on the tutum-docker-lamp folder:

	docker build -t kossoy/lamps .


Running your LAMP docker image
------------------------------

Start your image binding the external ports 80 and 3306 in all interfaces to your container:

	docker run --name <container name> -d -p 2222:22 -p 80:80 -p 3306:3306 -p 445:445 kossoy/lamps
	

Optional 3000, 9000 and 8080 ports.


Connecting to the ssh server 
-----------------------------

The first time that you run your container, a random password will be generated
for user `root`. To get the password, check the logs of the container by running:

	docker logs <CONTAINER_ID>

You will see an output like the following:

	========================================================================
	You can now connect to this Ubuntu container via SSH using:

	    ssh -p <port> root@<host>
	and enter the root password 'U0iSGVUCr7W3' when prompted

	Please remember to change the above password as soon as possible!
	========================================================================

In this case, `U0iSGVUCr7W3` is the password allocated to the `root` user.



Connecting to the bundled MySQL server from within the container
----------------------------------------------------------------

The bundled MySQL server has a `root` user with no password for local connections.
Simply connect from your PHP code with this user:

	<?php
	$mysql = new mysqli("localhost", "root");
	echo "MySQL Server info: ".$mysql->host_info;
	?>


Connecting to the bundled MySQL server from outside the container
-----------------------------------------------------------------

The first time that you run your container, a new user `admin` with all privileges 
will be created in MySQL with a random password. To get the password, check the logs
of the container by running:

	docker logs $CONTAINER_ID

You will see an output like the following:

	========================================================================
	You can now connect to this MySQL Server using:

	    mysql -uadmin -p47nnf4FweaKu -h<host> -P<port>

	Please remember to change the above password as soon as possible!
	MySQL user 'root' has no password but only allows local connections
	========================================================================

In this case, `47nnf4FweaKu` is the password allocated to the `admin` user.

You can then connect to MySQL:

	 mysql -uadmin -p47nnf4FweaKu

Remember that the `root` user does not allow connections from outside the container - 
you should use this `admin` user instead!


Setting a specific password for the MySQL server admin account
--------------------------------------------------------------

If you want to use a preset password instead of a random generated one, you can
set the environment variable `MYSQL_PASS` to your specific password when running the container:

	docker run -d -p 80:80 -p 3306:3306 -e MYSQL_PASS="mypass" tutum/lamp

You can now test your new admin password:

	mysql -uadmin -p"mypass"


Disabling .htaccess
--------------------

`.htacess` is enabled by default. To disable `.htacess`, you can remove the following contents from `Dockerfile`

	# config to enable .htaccess
    ADD apache_default /etc/apache2/sites-available/000-default.conf
    RUN a2enmod rewrite


Thanks to **http://www.tutum.co**
