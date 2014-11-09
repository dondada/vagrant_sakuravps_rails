# Vagrant/SakuraVPS Rails

This will provision a Rails box suitable for production sakura VPS use running Nginx, Unicorn and
MySQL on a CentOS 6.x server or local Vagrant environment.

It should allow you to have a development environment that is identical to your production environment
## Stack

+ Rails4
+ Nginx (under construction)
+ Unicorn (under construction)
+ Ruby2.1.2
+ Node.js
+ MySQL


## Requirements

+ vagrant 1.6.3 or higher
+ ansible 1.7.2 or higher

## Prepare for Development Server(local vagrant)

1. Run the commands below:

	```
        git clone <REPO_PATH>
        mv vagrant_sakuravps_rails <app_name>
        cd <app_name>
        vagrant up
        vagrant provision web
        vagrant ssh
        
        cd <Rails dir>
        gem install bundle 

        sudo chmod -R o+w /var/www
        rails new <app_name>
        cd <app_name>
        rails server -p 3000

	```
2. Append this line to /etc/sysconfig/iptables (as root)
       
	```
        -A INPUT -p tcp -m tcp --dport 3000 -j ACCEPT 
	```
3. restart iptables
       
	```
        sudo /etc/init.d/iptables
	```

4. start MySQL

	```
        ps ax | grep mysqld
        sudo service mysqld start
	```
       
5. access your web app 

	```
        using web browser, access http://localhost:8080  
	```

5. Let's coding !!


## Prepare for Production Server(Sakura VPS) 
## NOTE: this section is under construction... 

1. Run the commands below:

       
	```
        cd provision/ 

        change root password of sakura VPS manually

        using following command, you can create basis ( iptables, disallow root login , and so on...)
        $ user_password=$(openssl passwd -salt salty -1 \<user password\>)  
        $ ansible-playbook -k -c paramiko -i hosts sakura_root.yml -vv --extra-vars "user_password=$user_password"  
          
        create key pair for sakura VPS
        $ ssh-keygen -f ~/.ssh/sakura_rsa
        
        login sakura vps , and copy sakura vps's ssh-copy-id shell to local(/bin/ssh-copy-id)
        
        send your pub key to sakura vps
        $ ssh-copy-id -i ~/.ssh/sakura_rsa.pub "-p 10022 admin@\<SakuraServer ip address\>"  

        settings for ssh
        $ cat \<\<EOF \>\> ~/.ssh/config  
        Host sakura  
          Hostname \<SakuraServer ip address\>  
          Port 10022  
          User admin  
          IdentityFile ~/.ssh/sakura_rsa  
        EOF  

        ansible-playbook -i prod_hosts playbook_production.yml
	```

