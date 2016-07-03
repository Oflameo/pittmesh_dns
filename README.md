# Pittmesh DNS
Framework for implementing Distributed DNS.
Golden copy of the DNS records of Pittmesh

Ansible 2.0 or above is needed for initial provisioning. 
Works on Debian 8.

Ansible command invocation https://docs.ansible.com/ansible/playbooks_variables.html#passing-variables-on-the-command-line.

Bind resource records documentation http://www.bind9.net/dns-parameters. To modify bind resource records, modify db.pittmesh.j2. Don't modify any Ansible template variables or automatic provisioning will not work properly.
