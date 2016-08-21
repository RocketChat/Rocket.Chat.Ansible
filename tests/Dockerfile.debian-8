# Dockerfile.ubuntu
FROM debian:8

RUN systemctl mask -- \
    -.mount \
    dev-mqueue.mount \
    dev-hugepages.mount \
    etc-hosts.mount \
    etc-hostname.mount \
    etc-resolv.conf.mount \
    proc-bus.mount \
    proc-irq.mount \
    proc-kcore.mount \
    proc-sys-fs-binfmt_misc.mount \
    proc-sysrq\\\\x2dtrigger.mount \
    sys-fs-fuse-connections.mount \
    sys-kernel-config.mount \
    sys-kernel-debug.mount \
    tmp.mount \
 \
 && systemctl mask -- \
    console-getty.service \
    display-manager.service \
    getty-static.service \
    getty\@tty1.service \
    hwclock-save.service \
    ondemand.service \
    systemd-logind.service \
    systemd-remount-fs.service \
 \
 && ln -sf /lib/systemd/system/multi-user.target /etc/systemd/system/default.target \
 \
&& ln -sf /lib/systemd/system/halt.target /etc/systemd/system/sigpwr.target

RUN apt-get update -qq -y
RUN apt-get install -qq -y git python-pip python-dev libssl-dev \
	libffi-dev rsyslog systemd systemd-cron sudo

# Install Ansible
RUN sed -i 's/^\($ModLoad imklog\)/#\1/' /etc/rsyslog.conf
RUN pip install --upgrade ansible
# Install Ansible inventory file
# RUN mkdir -p /etc/ansible # Not really sure why this won't run but it doesn't
RUN bash -c 'mkdir -p /etc/ansible'
RUN echo "[local]\nlocalhost ansible_connection=local" > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup"]
VOLUME ["/run"]
CMD ["/sbin/init"]
