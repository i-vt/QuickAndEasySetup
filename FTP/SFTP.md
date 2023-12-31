# Copy/paste (non-root) user

```
sudo apt update -y

sudo apt-get install openssh-server -y


sudo apt-get install vsftpd -y
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.bak1
sudo wget "https://raw.githubusercontent.com/i-vt/QuickAndEasySetup/main/FTP/vsftpd.conf" -O /etc/vsftpd.conf


sudo apt-get  install ufw
sudo ufw enable
sudo ufw allow 21/tcp
sudo ufw allow 22/tcp
sudo ufw allow 30000:31000/tcp
sudo ufw reload


sftpuser="sftp_$(tr -dc 'a-z0-9' < /dev/urandom | head -c 20)"
sftppassword=$(tr -dc 'a-zA-Z0-9' < /dev/urandom | head -c 30)
sftpfilename="SFTP_Creds_$(tr -dc 'a-zA-Z0-9' < /dev/urandom | head -c 10).txt"



echo "Username: $sftpuser"
echo "Password: $sftppassword"


echo "Username: $sftpuser\n" > $sftpfilename
echo "Password: $sftppassword" >> $sftpfilename




sudo useradd $sftpuser -m -p "$(openssl passwd -1 $sftppassword)" -d "/home/$sftpuser/"
#sudo chsh -s /bin/false $sftpuser
#sudo chown $sftpuser:$sftpuser /home/$sftpuser

# Preferably log in with the new account :)

sudo mkdir -p /var/sftp/uploads
sudo chown -R $sftpuser:$sftpuser /var/sftp
sudo chmod 755 /var/sftp
sudo chown  $sftpuser:$sftpuser /var/sftp/uploads

sudo chown root:root /var/sftp
#sudo chmod 755 /var/sftp
#sudo usermod -a -G /var/sftp $sftpuser
#sudo chown $sftpuser:/var/sftp/*
#sudo chmod 775  -R /var/sftp/*


sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak1

sudo echo -e  "\n\nMatch User $sftpuser\n    ForceCommand internal-sftp\n    PasswordAuthentication yes\n    ChrootDirectory /var/sftp\n    PermitTunnel no\n    AllowAgentForwarding no\n    AllowTcpForwarding no\n    X11Forwarding no\n">>/etc/ssh/sshd_config


echo "ClientAliveInterval 60" | sudo  tee -a /etc/ssh/ssd_config
echo "KeepAlive yes" | sudo  tee -a /etc/ssh/ssd_config

sudo systemctl  restart sshd
sudo systemctl restart vsftpd

```
