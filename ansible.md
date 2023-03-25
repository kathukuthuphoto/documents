---
- name: Install and start Nginx on EC2 instance
  hosts: ec2-instance
  become: true
  vars:
    ansible_ssh_private_key_file: /path/to/your/private/key.pem
  tasks:
    - name: Install Python
      become: true
      apt:
        name: python
        state: present
    - name: Create /opt/python directory
      become: true
      file:
        path: /opt/python
        state: directory
    - name: Set ownership of /opt/python directory
      become: true
      file:
        path: /opt/python
        owner: root
        group: root
        mode: '0755'
    - name: Install pip
      become: true
      apt:
        name: python-pip
        state: present
    - name: Install virtualenv
      become: true
      pip:
        name: virtualenv
        state: latest
    - name: Create virtual environment
      become: true
      command: python -m venv /opt/python/myenv
    - name: Activate virtual environment
      become: true
      command: source /opt/python/myenv/bin/activate
    - name: Install Nginx
      become: true
      apt:
        name: nginx
        state: present
    - name: Install Docker
      become: true
      apt:
        name: docker.io
        state: present
    - name: Start Docker service
      become: true
      service:
        name: docker
        state: started
    - name: Add user to docker group
      become: true
      user:
        name: ubuntu
        groups: docker
        append: yes
    - name: Install Docker Python module
      become: true
      pip:
        name: docker
        state: latest
    - name: Start Nginx
      become: true
      service:
        name: nginx
        state: started
