**WEB STACK IMPLEMENTATION (LEMP STACK)**<h1>
Prerequisites<h2>
I used the following to complete this project:
1. A registered AWS account and a virtual server with Ubuntu Server OS.
2. Gitbash terminal

STEP 0<h3>
I started this implementation by creating and starting a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) image and created a new pem key.
I connected to my EC2 Instance using the GitBash, and it does not require conversion of .pem key to .ppk unlike puty.
so i used this command below on my GitBash to connect to my aws ec2 instance.(the private key i created is LEMP.pem)
    
		ssh -i <my-private-key.pem> ubuntu@<EC2-Public-IP-address>(ssh -i LEMP.pem ubuntu@ec2-35-177-0-242.eu-west-2.compute.amazonaws.com)
	
![1st lemp](https://user-images.githubusercontent.com/65473749/122800751-2db9ec80-d278-11eb-80c4-c524390bd5e1.png)

STEP 1: Installing the Nginx Web Server<h4>
AFter connecting to my Ec2 instance VM. I moved on to installing Nginx web server in order to display web pages to the site visitors.
by using the "apt"(meaning Advanced Package Tool) package manager to install the package.:
				
				$ sudo apt update
				$ sudo apt install nginx
 
When prompted for additional space to be used, i typed "Y"(means YES) to confirm my Nginx installation for it to run on my Ubuntu 20.04 server.
	![Nginx install](https://user-images.githubusercontent.com/65473749/122811372-411f8480-d285-11eb-9ffe-c2ac6e7357fb.png)

After the installation is done, i verified that Nginx web server was successfully installed and running as a service in my ubuntu, by running:
  		
			$ sudo systemctl status nginx
![Nginx config  status](https://user-images.githubusercontent.com/65473749/122813328-affddd00-d287-11eb-879a-0200665dfaeb.png)

So it showed active, running! in green color,which means that i did the installation correctly. I have just launched my first Web Server in the Clouds!
For me to be able to receive any traffic by the Web Server, i need to open TCP port 80 which is default port that web brousers use to access web pages in the Internet.

We have TCP port 22 open by default on my EC2 machine to access it via SSH, so i had to add a rule to EC2 configuration to open inbound connection through port 80:
by adding http type with protocol TCP 80 and Source 0.0.0.0/0 (Source 0.0.0.0/0 means ‘from any IP address’).
After adding the new rule, the server is running and i can access it locally and from the Internet.
	
Afterwards, i tried to check how we can access it locally in my Ubuntu shell, by running:

		$ curl http://localhost:80
					or
		$ curl http://127.0.0.1:80
These 2 commands above actually do the same,
- they use ‘curl’ command to request our Nginx on port 80. The difference is that: 
1. In the first case i tried to access the server via DNS name and  
2. The second one - by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called “resolution”).

I got this:
	![Nginx test locally-wcm to nginx](https://user-images.githubusercontent.com/65473749/122819665-96f92a00-d28f-11eb-81c2-bbfbfc30530b.png)


After checking how i can access it locally on my ubuntu shell. I then test how my Nginx server can respond to requests from the Internet by opening my web browser,
which is chrome and inputed the url below:
				
		http://<My-Public-IP-Address>:80 (http://3.8.87.239:80)
	
	This is a confirmation your server is successfully installed and running and accessible through firewall.
![Wlc to Nginx](https://user-images.githubusercontent.com/65473749/122817563-e9851700-d28c-11eb-89bb-d35e673b2c05.png)

STEP 2: Installing MySQL<h5>
Now that i have my web server running, i need a database managment system(DBMS) to be able to store and manage data for your site in a relational database which is MySQL.
To get this done, i used "apt" to acquire and install MySQL software on my ubuntu VM by running:

		$ sudo apt install mysql-server
	
When prompted,I confirmed the installation by typing Y, and then ENTER.
![MYSQL server install](https://user-images.githubusercontent.com/65473749/122821984-5b139400-d292-11eb-8b7d-26d7e3d86466.png)
![MYSQL INSTALL](https://user-images.githubusercontent.com/65473749/122820632-ac228880-d290-11eb-8979-77f34a31a5ac.png)
	
So i confirmed my installation by running:
	
	$ sudo systemctl status mysql-server
![MYSQL installation confirmation](https://user-images.githubusercontent.com/65473749/122822423-e68d2500-d292-11eb-94b2-c0ede212d77d.png)

When the installation is finished, i run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to the database system. 
I started the interactive script by running:

		$ sudo mysql_secure_installation 
![MYSQL secure installation](https://user-images.githubusercontent.com/65473749/122823994-e42bca80-d294-11eb-881a-5c7b5fa2a2b6.png)

	It asked if i want to configure the VALIDATE PASSWORD PLUGIN.
![MYSQL secure installation](https://user-images.githubusercontent.com/65473749/122823994-e42bca80-d294-11eb-881a-5c7b5fa2a2b6.png)
	
 I answered Y for yes, so i chose 0 for the password validation, i inputs my password and i got a prompt that the password is too weak. 
so i typed Y for yes. For the rest of the questions, i pressed Y and hit the ENTER key at each prompt.

After all this, i checked if am able to log in to the MySQL console by typing:
	
	$ sudo mysql
	
It connected to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running the command. 
The output below was displayd:
	
![sudo mysql output](https://user-images.githubusercontent.com/65473749/122825699-22c28480-d297-11eb-9e2e-dcd2e184a4b0.png)
	
MySQL server is now installed and secured. Next, i installed PHP, which is the final component in the LEMP stack.

STEP 3: Installing PHP<h5>
PHP is needed to be installed to process code and generate dynamic content for the web server(Nginx).

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. 
This allows for a better overall performance in most PHP-based websites, but it requires additional configuration.
So i had to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. 
And also install php-mysql, which is a PHP module that allows PHP to communicate with MySQL-based databases. 
I installed these 2 packages seperately, by first installing php-fpm:
	
	 $ sudo apt install php-fpm
![PHP-fpm install](https://user-images.githubusercontent.com/65473749/122827941-e3496780-d299-11eb-90cf-f46d13977d1d.png)

I moved on to installing the php-mysql:
	
	 $ sudo apt install php-mysql
![php-mysql install](https://user-images.githubusercontent.com/65473749/122831398-b21f6600-d29e-11eb-90f4-a334cbb0a51b.png)

I now have my PHP components installed. Next, I will have to configure Nginx to use them.

Step 4 — Configuring Nginx to Use PHP Processor<h6>
When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. 
In this project, I used projectLEMP as my domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. 
While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. 
Instead of modifying /var/www/html, i created a directory structure within /var/www for the projectLEMP website, 
leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

So i created the root web directory for my_domain by using:

		$ sudo mkdir /var/www/projectLEMP
	
![mk dir](https://user-images.githubusercontent.com/65473749/122835248-dd0cb880-d2a4-11eb-8e80-d84182717bb4.png)

Next, I assigned ownership of the directory with the $USER environment variable, which will reference my current system user:

	$ sudo chown -R $USER:$USER /var/www/projectLEMP
	
![mk dir](https://user-images.githubusercontent.com/65473749/122835248-dd0cb880-d2a4-11eb-8e80-d84182717bb4.png)

Then, open a new configuration file in Nginx’s sites-available directory using my preferred command-line editor. (you can use nano or vim) 
Here, i used vim because i encountered a problem while using NANO, which is "Error writing /etc/nginx/sites-available/projectLEMP: Permission denied " when trying to save it. 
So i tried using vim and i was able to save it without no permission denied:

When i tried using nano, i got this:

![Troubleshoot](https://user-images.githubusercontent.com/65473749/122836149-9029e180-d2a6-11eb-809b-23395f43680c.png)

then i used vim:

$ sudo vim /etc/nginx/sites-available/projectLEMP

This will create a new blank file. I Pasted this scripts in the bare-bones configuration:

	#/etc/nginx/sites-available/projectLEMP

	  server {
			listen 80;
			server_name projectLEMP www.projectLEMP;
			root /var/www/projectLEMP;

			index index.html index.htm index.php;

			location / {
					try_files $uri $uri/ =404;
			}

			location ~ \.php$ {
					include snippets/fastcgi-php.conf;
					fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
			 }

			location ~ /\.ht {
					deny all;
			}

	}


I activated my configuration by linking it to the config file from Nginx’s sites-enabled directory:

	$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
	
![vim n testin](https://user-images.githubusercontent.com/65473749/122845261-2286b100-d2b8-11eb-8e3f-09e7a6270a43.png)
	
This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

			$ sudo nginx -t

then i got this:
	
![vim n testin](https://user-images.githubusercontent.com/65473749/122845261-2286b100-d2b8-11eb-8e3f-09e7a6270a43.png)
	
I disabled the default Nginx host that is currently configured to listen on port 80, by running:

		$	sudo unlink /etc/nginx/sites-enabled/default
	
But i had error unlinking the config, file
![Screenshot (42)](https://user-images.githubusercontent.com/65473749/122848431-5bc21f80-d2be-11eb-91e6-92299396d121.png)

	
Then I reload Nginx to apply the changes:

		$ sudo systemctl reload nginx
	
My LEMP stack is now fully configured. The next step is to create a PHP script to test that Nginx is able to handle ".php" files within my newly configured website.

STEP 5: Testing PHP with Nginx<h7>
	
At this point, my LEMP stack is completely installed and fully operational.
So i created a PHP script to test and validate that Nginx is able to handle .php files by creating a test PHP file in my document root. 
I opened a new file called info.php within my document root in my text editor, with this:

			$ nano /var/www/projectLEMP/info.php
	
I typed the script below into the new file. (This is valid PHP code that will return information about a server):

			<?php
			phpinfo();

I then accessed the page in my web browser by visiting the public IP address(domain name can also be used) I set up in my Nginx configuration file, followed by /info.php:

			http://3.8.87.239:80/info.php

A web page containing detailed information about my server appeared on my brower:
![Screenshot (36)](https://user-images.githubusercontent.com/65473749/122847763-236e1180-d2bd-11eb-889d-ade078f46c58.png)

After checking the relevant information about my PHP server through the webpage, i removed the file i created because it contains sensitive information about my PHP environment and my Ubuntu server by using:

		$ sudo rm /var/www/your_domain/info.php

![remove php info](https://user-images.githubusercontent.com/65473749/122849273-0ab32b00-d2c0-11eb-940b-e0fab150070b.png)

STEP 6: Testing Database Connection from PHP (Optional)<h8>

To test whether PHP is able to connect to MySQL and execute database queries, I created a test database with simple “To do list” and configure access to it, 
so the Nginx website would be able to query data from the DB and display it.

I created a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP. 
And then, created a database named example_database and a user named example_user.

First, connect to the MySQL console using the root account:

		$ sudo mysql

![sudo mysql console](https://user-images.githubusercontent.com/65473749/122850471-1bfd3700-d2c2-11eb-8e5a-99754c16600b.png)

To create a new database, I run the following command from my MySQL console:

		mysql> CREATE DATABASE example_database;
 
I then create a new user and grant it full privileges on the custom database i just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. 
So i defined the user’s password as {password}.

		mysql>CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

![sudo mysql console (2)](https://user-images.githubusercontent.com/65473749/122851293-8ebae200-d2c3-11eb-90e1-1806369155bf.png)
 
Now we need to give this user permission over the example_database database:

		mysql>GRANT ALL ON example_database.* TO 'example_user'@'%';
 
This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.
I then exit the MySQL shell with:
					
					mysql> exit
THE OUTPUT: 

![sudo mysql console (2)](https://user-images.githubusercontent.com/65473749/122851293-8ebae200-d2c3-11eb-90e1-1806369155bf.png)

I tried testing if the new user has the proper permissions by logging in to the MySQL console again, this time i used the custom user credentials:

		mysql -u example_user -p

![user permission -pw](https://user-images.githubusercontent.com/65473749/122851949-ad6da880-d2c4-11eb-9f5f-4800cca7b3a3.png) 

Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user. so i inputed the password created
OUTPUT:

![user permission -pw](https://user-images.githubusercontent.com/65473749/122851949-ad6da880-d2c4-11eb-9f5f-4800cca7b3a3.png)

After logging in to the MySQL console, I confirmed that i have access to the example_database database:

				mysql> SHOW DATABASES;
 
THE OUTPUT:

![Shw database](https://user-images.githubusercontent.com/65473749/122852390-616f3380-d2c5-11eb-9523-27d89a789746.png)

Next, I created a test table named todo_list. From the MySQL console, by running the statements below:

			CREATE TABLE example_database.todo_list (
			mysql>     item_id INT AUTO_INCREMENT,
			mysql>     content VARCHAR(255),
			mysql>     PRIMARY KEY(item_id)
			mysql> );

![Shw database (2)](https://user-images.githubusercontent.com/65473749/122852970-54067900-d2c6-11eb-9c5c-f2f586186805.png)

Inserted a few rows of content in the test table:

			mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");

![Shw database (2)](https://user-images.githubusercontent.com/65473749/122852970-54067900-d2c6-11eb-9c5c-f2f586186805.png)

To confirm that the data was successfully saved to your table, run:

			mysql> SELECT * FROM example_database.todo_list;

THE OUTPUT:

![Shw database (2)](https://user-images.githubusercontent.com/65473749/122852970-54067900-d2c6-11eb-9c5c-f2f586186805.png)

After confirming that i have a valid data in my test table, i then exit the MySQL console.
 
Now I can create the PHP script that will connect to MySQL and query for my content. 
I created a new PHP file in my custom web root directory using you nano editor:

		nano /var/www/your_domain/todo_list.php
 
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, exhibiting the results in a list. 
(If there’s a problem with the database connection, it will throw an exception).

I copyied this content below into my todo_list.php script:

/var/www/your_domain/todo_list.php

			<?php
			$user = "example_user";
			$password = "password";
			$database = "example_database";
			$table = "todo_list";

			try {
				$db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
				echo "<h2>TODO</h2><ol>";
				foreach($db->query("SELECT content FROM $table") as $row) {
					echo "<li>" . $row['content'] . "</li>";
				}
				echo "</ol>";
			} catch (PDOException $e) {
					print "Error!: " . $e->getMessage() . "<br/>";
					die();
			}

![nano todolist-php](https://user-images.githubusercontent.com/65473749/122854450-9af56e00-d2c8-11eb-883b-d689ebc0a17d.png)


I Saved and closed the file when i finished editing.

I then access the page in my web browser by visiting the public IP address configured for my website, followed by /todo_list.php:

			http://3.8.87.239:80/todo_list.php

A page showing the content i inserted in my test table was displayed:

![Screenshot (45)](https://user-images.githubusercontent.com/65473749/122854950-55857080-d2c9-11eb-9b7b-0c25a233efd4.png)

That means my PHP environment is ready to connect and interact with my MySQL server.
