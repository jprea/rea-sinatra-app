# REA Practical Task

## Design Choices: 
AWS EC2 and Docker were used as I believe these tools are currently part of the REA workflow. Ansible was used due to familiarity and ease of deployment.

Code is separated into three scripts, so that provisioning, configuration, and deployment can all be run separately. 

## Prerequisites: 
* Git
* Ansible
* AWS Account

You will also need to install the following so that Ansible can talk with AWS:
```
pip install boto boto3
```

## Deployment:
```
git clone https://github.com/jprea/rea-sinatra-app.git
cd rea-sinatra-app
```

Create aws-keys.yml:
```
ansible-vault create config/aws-keys.yml
```
Add the following to the aws-keys.yml file, substituting in your AWS Keys where required. 
```
aws_access_key: your-access-key-here
aws_secret_key: your-secret-key-here
```
Save your Vault Password, as you will be asked for it later. 

Edit **config/specs.yml** and update *keypair* to your SSH Keypair. You may also need to update *region* if you are not able to deploy to us-east-2.

You should now be able to provision a new server as follows: 
```
ansible-playbook -i hosts --ask-vault-pass 001_provision.yml
```

An IP will be returned as part of the Ansible output and can be seen as *public_ip*. Add this IP to the bottom of the hosts file contained within the checked out directory under [webservers]. 

Next we want to configure the server: 
```
ansible-playbook -i hosts --ask-vault-pass 002_configure.yml
```

And lastly we want to deploy the Sinatra Application. 
```
ansible-playbook -i hosts --ask-vault-pass 003_deploy.yml
```

Once this has been done, you should be able to curl the IP Address which was listed during provisioning and see the text **Hello World!** printed. 
```
curl <ip_address>:80
```

## Comments:
The provisioning script will create a new server each time it is run. Previous servers will need to be cleaned up manually. 

Ideally the IP Address listed during server provisioning should be automatically added to the end of the hosts file.

