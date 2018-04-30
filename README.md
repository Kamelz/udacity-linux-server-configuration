1 - sudo yum update
2 - sudo nano /etc/ssh/sshd_config 
change port to 2200
remove #
sudo /etc/init.d/sshd restart
to make sure it works fine 
sudo netstat -tulpn |grep sshd
you should see this
tcp        0      0 0.0.0.0:2200                0.0.0.0:*                   LISTEN      8773/sshd
tcp        0      0 :::2200                     :::*                        LISTEN      8773/sshd

3- ufw:
 sudo yum install epel-release -y
 sudo yum install --enablerepo="epel" ufw -y
 sudo ufw enable 
  sudo ufw allow 2200/tcp
  sudo ufw allow 80/tcp
  sudo ufw allow 123/tcp

4-  sudo adduser grader
  sudo nano /etc/sudoers.d/grader
  paste and save
  grader ALL=(ALL) NOPASSWD:ALL

 5- ssh-keygen -t rsa

 6 - rm -f /etc/localtime
ln -s /usr/share/zoneinfo/UTC /etc/localtime

todo  Install and configure Apache to serve a Python mod_wsgi application.