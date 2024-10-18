# Ansible Collection for Kubernetes

![](https://i.imgur.com/waxVImv.png)
### [View all Roadmaps](https://github.com/nholuongut/all-roadmaps) &nbsp;&middot;&nbsp; [Best Practices](https://github.com/nholuongut/all-roadmaps/blob/main/public/best-practices/) &nbsp;&middot;&nbsp; [Questions](https://www.linkedin.com/in/nholuong/)
<br/>

Ansible Collection for [Kubernetes](https://github.com/kubernetes/kubernetes).

This Ansible collection provides Ansible playbooks and roles for the deployment and configuration of a [Certified Kubernetes](https://www.cncf.io/certification/software-conformance/) environment.

## Requirements

This collection require Ansible community package 4.10 or higher.

This collection was designed for:

-   Ubuntu 18.04, 20.04, 22.04, 22.10, 23.04
-   CentOS 7, 8 Stream, 9 Stream
-   openSUSE Leap 15.4, Tumbleweed
-   Debian 10, 11, Testing
-   Fedora 36, 37, 38, Rawhide
-   RHEL 7, 8, 9

## Quick Start

### Bootstrap Ansible and Roles

Start by cloning the repository, checkout the corresponding branch, and init with `git submodule`, then install Ansible (see <https://software.opensuse.org/download/package?package=ansible&project=home%3Aalvistack>):

    # GIT checkout development branch
    mkdir -p /opt/ansible-collection-kubernetes
    cd /opt/ansible-collection-kubernetes
    git init
    git remote add upstream https://github.com/nholuongut/ansible-collection-for-deploying-kubernetes.git
    git fetch --all --prune
    git checkout upstream/develop -- .
    git submodule sync --recursive
    git submodule update --init --recursive

    # Bootstrap Ansible
    echo 'deb http://downloadcontent.opensuse.org/repositories/home:/alvistack/xUbuntu_22.04/ /' | tee /etc/apt/sources.list.d/home:alvistack.list
    curl -fsSL https://downloadcontent.opensuse.org/repositories/home:alvistack/xUbuntu_22.04/Release.key | gpg --dearmor | tee /etc/apt/trusted.gpg.d/home_alvistack.gpg > /dev/null
    apt update
    apt install ansible

    # Confirm the version of Ansible
    ansible --version

### AIO

All-in-one (AIO) build is a great way to perform an Kubernetes build for:

-   A development environment
-   An overview of how all the Kubernetes services fit together
-   A simple lab deployment

Simply execule our default Molecule test case and it will deploy all default components into your localhost:

    # Run Molecule test case
    molecule test -s default

    # Confirm the version and status of Kubernetes
    kubectl version --output=yaml
    kubectl get node --output=yaml
    kubectl get pod --all-namespaces

### Production

In order to avoid [Single Point of Failure](https://en.wikipedia.org/wiki/Single_point_of_failure), at least 3 instances for Kubernetes is recommended.

For production environment we should backed with [Ceph File System](https://docs.ceph.com/docs/master/cephfs/) for [Kubernetes Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) with `ReadWriteMany` support. Corresponding dynamic provisioning could be handled by using [CSI CephFS](https://github.com/ceph/ceph-csi).

Traditionally we could use [Docker](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker) or [containerd](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd) as [Kubernetes container runtime (CRI)](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes/). Now a day, this collection is default with the modern [CRI-O](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cri-o) implementation.

Moreover, we are using [Cilium](https://cilium.io/) as [Kubernetes network plugin (CNI)](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/) so we could support [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

This deployment will setup the follow components:

-   [Kubernetes](https://kubernetes.io/)
    -   CRI: [CRI-O](https://github.com/cri-o/cri-o)
    -   CNI: [Cilium](https://github.com/cilium/cilium)
    -   CSI: [CSI CephFS](https://github.com/ceph/ceph-csi)
    -   Addons:
        -   [Kubernetes Dashboard](https://github.com/kubernetes/dashboard)
        -   [NGINX Ingress Controller](https://github.com/kubernetes/ingress-nginx)
        -   [Cert Manager](https://github.com/jetstack/cert-manager)

Start by copying the default inventory for customization:

    # Copy default inventory
    mkdir -p /etc/ansible
    rsync -av /opt/ansible-collection-kubernetes/inventory/default/ /etc/ansible

You should update the following files as per your production environment:

-   `/etc/ansible/hosts`
    -   Update with your inventory hostnames and IPs
-   `/etc/ansible/group_vars/all/*.yml`
    -   Update `*_release` and `*_version` if you hope to pin the deployment into any legacy supported version

Once update now run the playbooks:

    # Run playbooks
    cd /opt/ansible-collection-kubernetes
    ansible-playbook playbooks/converge.yml
    ansible-playbook playbooks/50-kube-verify.yml
    ansible-playbook playbooks/60-kube_cilium-install.yml
    ansible-playbook playbooks/70-kube_csi_cephfs-install.yml
    ansible-playbook playbooks/70-kube_csi_cephfs-verify.yml
    ansible-playbook playbooks/80-kube_dashboard-install.yml
    ansible-playbook playbooks/80-kube_ingress_nginx-install.yml
    ansible-playbook playbooks/80-kube_cert_manager-install.yml

    # Confirm the version and status of Kubernetes
    kubectl version --output=yaml
    kubectl get node --output=yaml
    kubectl get pod --all-namespaces

### Molecule

You could also run our [Molecule](https://molecule.readthedocs.io/en/stable/) test cases if you have [Vagrant](https://www.vagrantup.com/) and [Libvirt](https://libvirt.org/) installed, e.g.

    # Run Molecule on Ubuntu 22.04
    molecule converge -s ubuntu-22.04

Please refer to [.gitlab-ci.yml](.gitlab-ci.yml) for more information on running Molecule.

# 🚀 I'm are always open to your feedback.  Please contact as bellow information:
### [Contact ]
* [Name: nho Luong]
* [Skype](luongutnho_skype)
* [Github](https://github.com/nholuongut/)
* [Linkedin](https://www.linkedin.com/in/nholuong/)
* [Email Address](luongutnho@hotmail.com)

![](https://i.imgur.com/waxVImv.png)
![](Donate.png)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/nholuong)

# License
* Nho Luong (c). All Rights Reserved.🌟
