# Ansible Nginx Deployment Project

This repository contains **Ansible playbooks** and supporting files for provisioning and configuring an Nginx web server on one or more Amazon Linux hosts from a control machine (your laptop, jump-box, or dedicated Ansible controller).

---

## 📂 Project Structure

| Path | Purpose |
|------|---------|
| `index.html` | Sample web page that will be deployed to the web‑root. |
| `instruction.txt` | Raw checklist that the playbook was built from (now merged into this README). |
| `key.pkk` | **Host (control‑node) key** – used _by the controller_ when connecting **outward** from your machine (keep it safe). |
| `key.pem` | **Managed host key** – SSH private key that allows Ansible to log in to the EC2 instances you are configuring. |
| `myinventory.yml` | Static inventory file that defines the group `mynginx` with one or more target IPs or hostnames. |
| `nginx_setup.yml` | Main playbook: installs Nginx, copies `index.html`, opens the firewall, and starts the service. |
| `vars.yml` | Variable file read in at runtime with `--extra-vars "@vars.yml"` so you can change server names, document root, etc. |

---

## 🛠️ Prerequisites

1. **Ansible installed on the control node.**

   <details>
   <summary>Amazon Linux 2</summary>

   ```bash
   sudo yum update -y
   sudo amazon-linux-extras enable epel
   sudo yum install epel-release -y
   sudo yum install ansible -y
   ansible --version
   ```
   </details>

   <details>
   <summary>Ubuntu / Debian</summary>

   ```bash
   sudo apt update
   sudo apt install ansible -y
   ansible --version
   ```
   </details>

2. **SSH key permissions.**

   Copy or generate the private key that matches the public key on your EC2 instances into the project root **as `key.pem`** and lock down the permissions:

   ```bash
   chmod 400 key.pem
   ```

3. **Inventory & variables adjusted.**

   - Edit `myinventory.yml` if your hosts/group names differ.
   - Edit `vars.yml` and replace the placeholder IP addresses with your **public instance IPs**:

     ```bash
     nano vars.yml   # or your preferred editor
     ```

---

## 🚀 Quick Start

```bash
# Ping the hosts to confirm Ansible can reach them
ansible mynginx   -m ping   -i myinventory.yml   --private-key key.pem   --extra-vars "@vars.yml"

# Execute the full playbook
ansible-playbook   -i myinventory.yml nginx_setup.yml   --private-key key.pem   --extra-vars "@vars.yml"
```

---

## 📝 Step‑by‑Step Walk‑through (from `instruction.txt`)

| Step | Action |
|------|--------|
| **A** | Prepare control node (see **Amazon Linux 2** commands above). |
| **B** | Copy private key into **`key.pem`** and run **`chmod 400 key.pem`**. |
| **C** | Update target IPs in **`vars.yml`**, then test connectivity:<br/>`ansible mynginx -m ping -i myinventory.yml --extra-vars "@vars.yml"` |
| **D** | Run the playbook:<br/>`ansible-playbook -i myinventory.yml nginx_setup.yml --extra-vars "@vars.yml"` |

---

## 🔐 Key Mapping

```
Control‑node (your machine)  -> key.pkk  (private key you own)
Managed EC2 instances        -> key.pem  (ssh key that exists on instances)
```

Keep both keys **private**; never commit them to a public repository.

---

## 📑 What the Playbook Does

1. Installs the latest stable Nginx package.
2. Copies `index.html` to `/usr/share/nginx/html/index.html`.
3. Ensures the Nginx service is enabled and started.
4. (Optional) Opens TCP 80 in the OS firewall if required.

You can tweak behaviour by editing `vars.yml` or directly adjusting `nginx_setup.yml`.

---

Happy automating!