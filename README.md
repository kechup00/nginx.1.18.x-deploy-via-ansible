INSTALL/TEST/REMOVE NGINX VIA ANSIBLE ON LOCALHOST

tested on :

    ansible 2.9.6
    python version = 3.8.10
    #49~20.04.1-Ubuntu with:
     - nginx version: nginx_1.18.0-1~focal_amd64.deb








main_playbook.yml = facts -> install nginx -> test nginx -> remove nginx 

command = ansible-playbook main_playbook.yml -i hosts-test.yml -b -K -l local --diff --flush-cach


tags-list below :
(eg: -t install_nginx )

playbook: main_playbook.yml

  play #1 (all): Run facts	TAGS: [facts]
      TASK TAGS: [facts]

  play #2 (all): Install and configure nginx	TAGS: [install_nginx]
      TASK TAGS: [install_nginx]

  play #3 (web_servers): Test nginx	TAGS: [test_nginx]
      TASK TAGS: [test_nginx]

  play #4 (local): Remove nginx	TAGS: [remove_nginx]
      TASK TAGS: [remove_nginx]




1) To run the main playbook main_playbook.yml you need to start the command with "ansible-playbook" followed by the file name "main_playbook.yml"   

( eg: ansible-playbook main_playbook.yml ) 


2) The inventory files is hosts-test.yml you need to  specify  the inventory with "-i" 

( eg: -i hosts-test.yml)

3) In case you need to run the main_playbook.yml on a specific group/host you need to use the limit option with "-l" 

( eg: -l local )

4) to run playbook as root use "-b" option along with ask for password "-K"

5) For dry-run use "-C" or for RocKandRoll without :)  

6) To flash the cache use "--flush-cache" option .



Per tag plays below:
------------------------------------------------------------------------------------------------------


command (1)  :  

ansible-playbook main_playbook.yml -i hosts-test.yml -b -K -l local --diff -t install_nginx --flush-cache

tasks:

- install nginx service
- start the service
- render the config file from j2 temp. 
- copy config to /etc/nginx
- create new directory for the ssl certificates /etc/nginx/cert/  (the certs are in the main directory)
- copy the certs in the /usr/local/share/ca-certificates/ and wait for 5 sec
- reload the local cert list
- restart the service ,  if need it .



-------------------------------------------------------------------------------------------------------


command (2)  :  

ansible-playbook main_playbook.yml -i hosts-test.yml -b -K -l local --diff -t test_nginx --flush-cache 

tasks: 
- check for curl package , if not , install the latest version
- run curl command to test for the nginx server status page  using HTTPS and port 8080
- run a nestat command and grep for 127.0.0.1 to check if the servers is listening on the right port
- check if the firewall is active ( ufw )




-------------------------------------------------------------------------------------------------------
!!! Please remove the "-C" from the command below !!!

command (3)  :  

ansible-playbook main_playbook.yml -i hosts-test.yml -b -K -l local --diff -t remove_nginx --flush-cache -C

tasks:
- remove nginx package with the following options: [ purge, autoremove, autoclean, update_cache ] 
- remove certifcate nginx-certificate.crt from /usr/local/share/ca-certificates/
- remove certifcate nginx.key from /usr/local/share/ca-certificates/
- reload the local cert list 



-------------------------------------------------------------------------------------------------------- 



















