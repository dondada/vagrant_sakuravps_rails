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
        
        cd /var/www/rails
        gem install bundle 

        sudo chmod -R o+w /var/www
        rails new <app_name>

	```

2. Install unicorn using gem
       
	```
        sudo gem install unicorn
	```

3. open up a blank unicorn.rb document, which will be saved inside config/ directory
       
	```
        vim config/unicorn.rb
	```

4. Place the below block of code. I recommend the following basic configuration:
       
        see https://gist.github.com/tjnet/e84578aed3d72d617457

    Note: To test your app with unicorn, run unicorn_rails inside the application directory

5. Append this line to /etc/sysconfig/iptables (as root)
       
	```
        -A INPUT -p tcp -m tcp --dport 3000 -j ACCEPT 
	```
6. restart iptables
       
	```
        sudo /etc/init.d/iptables
	```

7. start MySQL

	```
        ps ax | grep mysqld
        sudo service mysqld start
	```
       
8. access your web app 

	```
        cd /var/www/<app_name>
        unicorn_rails -c config/unicorn.rb -E development -D
        ps -ef | grep unicorn
        using web browser, access http://localhost:8080  
	```


9. Let's coding !!

## Prepare for Production Server(Sakura VPS) 
## NOTE: this section is under construction... 

1. Run the commands below:

       
	```
        cd provision/ 

        change root password of sakura VPS manually

        using following command, you can create basis ( iptables, disallow root login , and so on...)
        $ user_password=$(openssl passwd -salt salty -1 \<user password\>)  
        $ ansible-playbook -k -c paramiko -i prod_hosts sakura_root.yml -vv --extra-vars "user_password=$user_password"  
          
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
## Configuring Unicorn

#### How to launch a unicorn server


	```
        unicorn_rails -c config/unicorn.rb -E development -D
	```

#### How to stop a unicorn server process from running


       In terminal "ps -ef | grep unicorn" and have a look for the Master Unicorn Process.
       And then type "kill -9 <Master Unicorn Process>"


