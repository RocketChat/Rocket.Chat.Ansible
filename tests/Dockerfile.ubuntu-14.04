# Dockerfile.ubuntu
FROM ubuntu-upstart:14.04
# Install Ansible
#RUN mv /sbin/initctl.distrib /sbin/initctl
RUN apt-get update -y
RUN apt-get install -y python-software-properties software-properties-common
RUN add-apt-repository -y ppa:ansible/ansible
RUN apt-get update -y
RUN apt-get install -y ansible git-core
# Install Ansible inventory file
RUN echo "[local]\nlocalhost ansible_connection=local" > /etc/ansible/hosts
