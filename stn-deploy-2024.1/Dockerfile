FROM rockylinux:9.3

RUN dnf -y update && \
    dnf -y install gcc git iputils jq libffi-devel openssl-devel perl-Digest-SHA procps-ng python3-devel python3-libselinux rsync sshpass unzip wget which && \
    dnf clean all && \
    rm -rf /var/cache/yum

# kolla-ansible has specific dependencies on the ansible versions
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir --upgrade 'ansible-core>=2.15,<2.16.99' && \
    pip install --no-cache-dir openstackclient

# checkout kolla-ansible
RUN git clone -b stable/2024.1 https://opendev.org/openstack/kolla-ansible.git /tmp/kolla-ansible

# patch kolla-ansible if needed
ADD patches /tmp/patches
RUN cd /tmp/kolla-ansible && git apply --allow-empty -v /tmp/patches/*.patch

# install patched kolla-ansible
RUN pip install --no-cache-dir /tmp/kolla-ansible

# create config file dirs
RUN mkdir -p /etc/kolla && \
    mkdir -p /etc/ansible

# copy ansible.cfg to container
COPY assets/ansible.cfg /etc/ansible/ansible.cfg

# install openstack.cloud collection
RUN ansible-galaxy collection install openstack.cloud

# Install kolla-ansible dependencies
RUN kolla-ansible install-deps

WORKDIR /deploy

