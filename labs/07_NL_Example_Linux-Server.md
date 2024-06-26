# Lab 7: Extra - Apache Servers
  
## Task 7.1: Apache installeren

De eerste stap is de webserver software installeren. Na installatie moet de webserver natuurlijk gestart worden. Beide acties voeren we uit in het onderstaande playbook.

* Maak het playbook ``linux.yml``:

  ```
  ---
  - hosts: workshop
    become: true
    become_method: sudo

    tasks:
      - name: Install apache packages
        yum:
          name: httpd
          state: present
        tags: install

      - name: ensure httpd is running
        service:
          name: httpd 
          state: started
        tags: install
  ```

* Voer het playbook uit:

  ``$ ansible-playbook linux.yml``
  
## Task 7.2: Firewall configureren

Om de webserver goed te laten werken, dient poort 80 (http) open gezet te worden. Hiervoor gebruiken we de Ansible module ``firewalld``. Om er zeker van te zijn dat de nieuwe regel ingelezen wordt, herstarten we ``firewalld`` na de wijziging. 
 
* Pas het playbook ``linux.yml`` aan:
 
  ```
      - name: Ensure port 80 is open for http access
        firewalld:
          service: http
          permanent: true
          state: enabled
        tags: configure

      - name: Ensure firewalld service is restarted after the firewall changes
        service: 
          name: firewalld 
          state: restarted
        tags: configure
   ```

* Voer het playbook uit met alleen de configure tag:

  ``$ ansible-playbook linux.yml --tags configure``
   
## Task 7.3: Installeer content voor de webserver

  
* Pas het playbook ``linux.yml`` aan:

  ```
      - name: Ensure content is installed in the webserver
        copy:
          content: "Hello World! This is the Ansible Basic Workshop Extra Lab on the {{ ansible_facts.hostname }} Linux Server\n"
          dest: /var/www/html/index.html
          owner: apache
          group: apache
          mode: 0644
        tags: content
  ```
    
* Voer het playbook uit:

  ``$ ansible-playbook linux.yml --tags content``
  
* Open in je browser de url ``http://<hostname>.<domain-name>`` (vervang ``<hostname>`` door het de hostname van de Linux server).
