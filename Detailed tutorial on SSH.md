# Detailed tutorial on SSH

#### Simple understanding of how SSH works: 
1. When the server installs an openssh-server and you install an openssh-client on your own computer, you can simply log in via ssh for remote control

2. When logging in, you need to know the account name, ip address of the server, and the port of the server, then run: 
        ssh {user_account}@host_ip_address -p{port_num}  

3. More details: public keys could replace the password for password-free logging in. Each connection will add the host keys of the server into the client for future check  


#### Different levels to work around ssh, depending on your familiarity and demand with ssh, 
1. If you do not mind log in with other own account and do not want to get into detail, you can just follow guide 1 to install openssh-client and connect to server using others' account (certainly under others' permission)

2. If you want to create your own account on the server via ssh, follow guide 2 to create your account(will be using others' account temporarily), and folllow 1.3 to connect to the server

3. If you want to log in your own account wighout password(which is safer), follow guide 3 to generate public key


#### If you encount any problems, check the problem solving in Guide 5 to see if any solution already existed


#### Note: A common mis-manipulation is to run "ssh-keygen" on the server side, which shoule actually be done on your own computer to generate public key, but would affect others' use of ssh if the command is run on server.


#### Guide 1: if you are a client connecting to the server: (all commands are run on your local computer)
    1. (first time using ssh) check if ssh client is installed on local computer:
        ssh
    2. (first time using ssh) install ssh client:
        sudo apt-get install openssh-client
    3. connect to server:
        ssh {user_account}@host_ip_address -p{port_num}

    Detailed intro to ssh (including how to install): 
        https://phoenixnap.com/kb/ssh-to-connect-to-remote-server-linux-or-windows
        
#### Guide 2: if you want to create your own account on the server:
    1. log in the server by an existing account: (On your own computer)
        ssh {existing_user}@{host_ip_address} -p{port_num}
    2. add new user: (on the server)
        sudo useradd -s /bin/bash -d /home/{new_user}/ -m -G sudo {new_user}
    3. set the password for the new user: (on the server)
        sudo passwd {new_user}
    4. (optional) if you mis-create your account, you can delete your account as below, and go back to step 2: (on the server)
        sudo deluser --remove-home {newuser}
    5. (Note) Do not run ssh-keygen on the server side, this could changed the host keys and others users could not log in

    Detailed explanation on adding account: 
        https://www.cyberciti.biz/faq/create-a-user-account-on-ubuntu-linux/
    
#### Guide 3: If you want to log in without password by setting up public key: (all commands are run on your local computer)
    1. Create RSA key on your own computer:
        mkdir -p $HOME/.ssh
        chmod 0700 $HOME/.ssh
        ssh-keygen -t rsa (Attention: this has been be run on your own computer, rather than on the sever, otherwise others users could not log in)

    2. Send RSA key to the server:
        ssh-copy-id -i ~/.ssh/id_rsa.pub {your_user}@{host_ip_address}
    3. log in via ssh free of passwd:
        ssh {your_user}@{host_ip_address} -p{port_num}

    Detailed explanation on logging without password via public key:
        https://www.cyberciti.biz/faq/ubuntu-18-04-setup-ssh-public-key-authentication/


#### Guide 4: if your are managing the server and any thing goes wrong, you can check:
    1. Whether the remote computer is turned on, and has a network connection.
    2. SSH server applications need to be installed and enabled.
        1. check if ssh server is installed: 
            ssh localhost
        2. install ssh server: 
            sudo apt-get install openssh-server ii
    3. You need the IP address or the name of the remote machine you want to connect to.
        IP address: hostname -I
        SSH port: sudo vim /etc/ssh/sshd_config (then serch for "port", update the port if updated)
    4. check the host keys fingerprint as in etc/ssh/ssh_host_rsa_key
        ssh-keygen -l -f ssh_host_rsa_key.pub
        ssh-keygen -l -f ssh_host_dsa_key.pub
        ssh-keygen -l -f ssh_host_ed25519_key.pub
        ssh-keygen -l -f ssh_host_ecdsa_key.pub
    5. Firewall settings need to allow the remote connection.
