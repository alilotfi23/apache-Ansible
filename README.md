# Ansible Playbook: Install Apache and Deploy Sample Website

## Overview
This Ansible playbook automates the Apache web server installation and deploys a sample website on a specified server.

## Requirements
- Ansible installed on the control node.
- Target server accessible via SSH.
- Internet connectivity on the target server for package downloads.

## Usage
1. Update the `hosts` file with your target server's hostname or IP address under `[serverhostname]`.

2. Customize variables if needed:
   - Modify the `url` parameter in the `get_url` task to point to your desired sample website ZIP file.
   - Adjust paths (`dest`, `src`) in tasks as per your server's filesystem layout.

3. Run the playbook:
   ```bash
   ansible-playbook -i hosts playbook.yml
   ```

## Playbook Structure
```yaml
- name: install apache and config html page
  gather_facts: no
  hosts: serverhostname
  tasks:
    - name: download sample website page
      get_url:
        url: https://www.free-css.com/assets/files/free-css-templates/download/page275/hangover.zip
        dest: /home/user1/

    - name: install unzip
      yum:
        name: unzip
        state: latest

    - name: unarchive zip
      unarchive:
        src: /home/user1/hangover.zip
        dest: /home/user1/
        remote_src: yes

    - name: install apache on ((ansible_all_ipv4_addresses))
      yum:
        name: httpd
        state: latest

    - name: open website port
      firewalld:
        service: http
        permanent: true
        state: enabled

    - name: copy html file on the html dir
      copy:
        src: /home/user1/hangover-master
        dest: /var/www/html/
        remote_src: yes
        directory_mode: yes

    - name: start apache services
      service:
        name: httpd
        enabled: yes
        state: started

    - name: restart firewall
      service:
        name: firewalld
        state: restarted
```

## Notes
- Ensure firewall rules permit incoming HTTP traffic (`port 80`) to access the website.
- Adjust paths and server configurations according to your specific setup and security policies.
- This playbook assumes CentOS/RHEL as the target server OS due to `yum` package manager usage.
