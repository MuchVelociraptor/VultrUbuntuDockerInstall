#!/bin/sh
USER_NAME=RaptorJesus

echo "Updating System"
apt-get update
apt-get upgrade -y
echo "Installing Docker"
apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose

echo "Adding new user"
useradd -p ! -m -G docker -s /bin/bash -c ${USER_NAME} ${USER_NAME}
echo "${USER_NAME}  ALL=NOPASSWD: ALL" >> /etc/sudoers
rsync --archive --chown=${USER_NAME}:${USER_NAME} /root/.ssh /home/${USER_NAME}

echo "Securing system"
rm -Rf /root/.ssh
usermod -p ! root
sed -i "s/.*RSAAuthentication.*/RSAAuthentication yes/g" /etc/ssh/sshd_config
sed -i "s/.*PubkeyAuthentication.*/PubkeyAuthentication yes/g" /etc/ssh/sshd_config
sed -i "s/.*PasswordAuthentication.*/PasswordAuthentication no/g" /etc/ssh/sshd_config
sed -i "s/.*AuthorizedKeysFile.*/AuthorizedKeysFile\t\.ssh\/authorized_keys/g" /etc/ssh/sshd_config
sed -i "s/PermitRootLogin.*/PermitRootLogin no/g" /etc/ssh/sshd_config
apt update
apt upgrade -y
apt autoremove -y
echo "Rebooting now!"
reboot
