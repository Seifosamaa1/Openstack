# 🚀 OpenStack RDO Deployment on Rocky Linux

This project demonstrates a full step-by-step deployment of **OpenStack Yoga** using the **RDO (Red Hat Distribution of OpenStack)** on **Rocky Linux / CentOS Stream 8**.

---

## 📋 Project Overview

This guide automates the setup of a single-node OpenStack controller using **Packstack**. It includes full instructions for:

- Host system preparation
- Networking and firewall setup
- OpenStack package installation
- Packstack answer file configuration
- Accessing and validating via Horizon Dashboard

---

## 🧱 Host Preparation

1. **OS Image**: Use CentOS Stream 8 or Rocky Linux.
2. **Virtualization**: Must be enabled on the host.
3. **Network**:
   - Two NICs (e.g., `ens192`, `br-ex`)
   - Static IP configuration
   - IPv6 disabled

4. **Firewall and NetworkManager**:
   ```bash
   systemctl disable --now firewalld
   systemctl disable --now NetworkManager
   ```

5. **Network Scripts**:
   ```bash
   dnf install -y network-scripts
   systemctl enable --now network
   ```

6. **Hostname and SELinux**:
   ```bash
   hostnamectl set-hostname controller
   setenforce 0
   sed -i 's/SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
   ```

---

## ⚙️ OpenStack Installation Steps

1. **Enable Repos**:
   ```bash
   dnf config-manager --enable powertools
   dnf install -y centos-release-openstack-yoga
   dnf -y update
   ```

2. **Install Packstack**:
   ```bash
   dnf install -y openstack-packstack
   ```

3. **Generate and Edit Answer File**:
   ```bash
   packstack --gen-answer-file=/root/answers.txt \
     --os-neutron-l2-agent=openvswitch \
     --os-neutron-ml2-mechanism-drivers=openvswitch \
     --os-neutron-ml2-tenant-network-types=vxlan \
     --os-neutron-ml2-type-drivers=vxlan,flat \
     --provision-demo=n \
     --os-neutron-ovs-bridge-mappings=extnet:br-ex \
     --os-neutron-ovs-bridge-interfaces=br-ex:ens192 \
     --keystone-admin-passwd=redhat \
     --os-heat-install=n
   ```

4. **Run the Installer**:
   ```bash
   packstack --answer-file=/root/answers.txt -t 3600
   ```

---

## ✅ Validation

- Access the **Horizon Dashboard** from your browser:
  ```
  http://<br-ex_IP>/dashboard
  ```

- Log in with the admin credentials and verify that services are up and functional.

---


## 📁 Resources

- [CentOS Stream ISO](https://dakhli-permenant.oss.eu-west-0.prod-cloud-ocb.orange-business.com/ITI_Course/CentOS-Stream-8-x86_64-20230112-dvd1.iso)

---

## 🛠️ Notes

- Ensure that `br-ex` is properly mapped to the external NIC.
- This setup is for learning or PoC purposes. For production, a multi-node HA setup is recommended.
