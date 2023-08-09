roles/
└── jenkins/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    └── templates/
        └── jenkins-config.xml.j2
---
- name: Update apt cache
  apt: update_cache=yes

- name: Install Java
  apt:
    name: openjdk-11-jdk
    state: present

- name: Add Jenkins repository key
  apt_key:
    url: https://pkg.jenkins.io/debian/jenkins.io.key
    state: present

- name: Add Jenkins repository
  apt_repository:
    repo: deb http://pkg.jenkins.io/debian-stable binary/
    state: present

- name: Install Jenkins
  apt:
    name: jenkins
    state: present

- name: Configure Jenkins
  template:
    src: jenkins-config.xml.j2
    dest: /etc/jenkins/jenkins-config.xml
  notify: Restart Jenkins

- name: Start Jenkins
  systemd:
    name: jenkins
    state: started
    enabled: yes

---
- hosts: aws
  roles:
    - jenkins
