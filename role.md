## What is an Ansible Role?

- “Ansible Role is a pre-defined folder structure that keeps your tasks, handlers, templates, files, variables all in an organized way."
- Ansible Roles are expressed in YAML—a human-readable data serialization language used to write configuration files
- https://www.redhat.com/en/topics/automation/what-is-an-ansible-role

## Role Folder Structure
```css
myrole/
 ├── tasks/
 │     └── main.yml
 ├── handlers/
 │     └── main.yml
 ├── files/
 ├── templates/
 ├── vars/
 ├── defaults/
 ├── meta/
```


- tasks/ → main tasks of the role

- handlers/ → restart/reload actions

- files/ → static files

- templates/ → Jinja2 template files

- vars/ → role-specific variables

- defaults/ → default variables

- meta/ → dependencies


## STEP 1 — Create Role 
Automatically creates the role folder structure.
```bash
ansible-galaxy init nginx_role
```
## STEP 2 — Edit tasks/main.yml
```bash
nano nginx_role/tasks/main.yml
```
```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present
  become: yes

- name: Start NGINX
  service:
    name: nginx
    state: started
    enabled: yes

- name: Create index file
  copy:
    dest: /var/www/html/index.html
    content: "<h1>Hello From Ansible Role</h1>"
  notify: restart nginx

```

## STEP 3 — Edit handlers/main.yml
runs only if file changes
```bash
nano nginx_role/handlers/main.yml
```
```yaml
---
- name: restart nginx
  service:
    name: nginx
    state: restarted
```

## STEP 4 — Create main playbook (site.yml)
```bash
nano site.yml
```

```yaml
---
- name: Run NGINX Role
  hosts: all
  become: yes

  roles:
    - nginx_role
```

## STEP 5 — Run the playbook
```bash
ansible-playbook site.yml
```
- First time → handler will run
- Second time → handler will NOT run because no change


## STEP 6 — Test handler triggers again
Change content in tasks: 
- content: Hello Again! Handler Test!
```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present
  become: yes

- name: Start NGINX
  service:
    name: nginx
    state: started
    enabled: yes

- name: Create index file
  copy:
    dest: /var/www/html/index.html
    content: "<h1>Hello Again! Handler Test!</h1>"
  notify: restart nginx
```
### Run ansible-playbook site.yml
```bash
ansible-playbook site.yml
```

![handler](./images/handler.png)






























