# Homework Assignment: 08. Ansible Workshop


## Homework Assignment 1: Configuration Management

### Project Directory Structure
```
08.Ansible.Workshop/
├── inventory.yml
├── playbook.yml
├── templates/
│   ├── index.html.j2
│   └── nginx_vhost.conf.j2
└── README.md
```

### Key Automation Features
* **Jinja2 Templates:** Configures separate Nginx server blocks automatically using dynamic parameters (`templates/nginx_vhost.conf.j2`).
* **Dynamic Fact Gathering:** Uses Ansible’s built-in fact-gathering mechanism to generate unique `index.html` landing pages (`templates/index.html.j2`).
Each site automatically embeds the remote server's specific system metrics:
  * **Managed Host Name** (`ansible_hostname`)
  * **Managed Host FQDN** (`ansible_fqdn`)
* **Loops and Handlers:** Optimizes infrastructure-as-code patterns by looping configuration blocks and utilizing an automated Nginx service restart handler only when syntax-safe changes are applied.


### How to Run the Playbook
Execute the following command from your control node to provision the remote targets defined in your YAML inventory:

```bash
ansible-playbook -i inventory.yml playbook.yml


PLAY [Homework 1 - Nginx Configuration Management with VirtualHosts] ***********************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************
[WARNING]: Host 'remote_target' is using the discovered Python interpreter at '/usr/bin/python3.10', but future
installation of another Python interpreter could cause a different interpreter to be discovered. See https://docs.ansible.com/ansible-core/2.20/reference_appendices/interpreter_discovery.html for more information.
ok: [remote_target]

TASK [Ensure Nginx is installed] ***********************************************************************************************************************
ok: [remote_target]

TASK [Create document root directories for virtualhosts] ***********************************************************************************************
ok: [remote_target] => (item={'domain': 'site1.local'})
ok: [remote_target] => (item={'domain': 'site2.local'})

TASK [Deploy index.html from Jinja2 template] **********************************************************************************************************
[WARNING]: Deprecation warnings can be disabled by setting `deprecation_warnings=False` in ansible.cfg.
[DEPRECATION WARNING]: INJECT_FACTS_AS_VARS default to `True` is deprecated, top-level facts will not be auto injected after the change. This feature will be removed from ansible-core version 2.24.
Origin: /home/max/Project/08.Ansible.Workshop/templates/index.html.j2

Use `ansible_facts["fact_name"]` (no `ansible_` prefix) instead.

ok: [remote_target] => (item={'domain': 'site1.local'})
ok: [remote_target] => (item={'domain': 'site2.local'})

TASK [Deploy Nginx virtualhost configurations] *********************************************************************************************************
ok: [remote_target] => (item={'domain': 'site1.local'})
ok: [remote_target] => (item={'domain': 'site2.local'})

TASK [Enable virtualhost sites via symlink] ************************************************************************************************************
ok: [remote_target] => (item={'domain': 'site1.local'})
ok: [remote_target] => (item={'domain': 'site2.local'})

TASK [Run Nginx syntax verification check] *************************************************************************************************************
ok: [remote_target]

TASK [Display Nginx Validation Report] *****************************************************************************************************************
ok: [remote_target] => {
    "msg": [
        "---------------- NGINX VALIDATION REPORT ----------------",
        [
            "nginx: the configuration file /etc/nginx/nginx.conf syntax is ok",
            "nginx: configuration file /etc/nginx/nginx.conf test is successful"
        ],
        "---------------------------------------------------------"
    ]
}

TASK [Remove default Nginx site configuration] *********************************************************************************************************
ok: [remote_target]

TASK [Verify HTTP availability for each VirtualHost] ***************************************************************************************************
ok: [remote_target] => (item={'domain': 'site1.local'})
ok: [remote_target] => (item={'domain': 'site2.local'})

TASK [Display Website Status Report] *******************************************************************************************************************
ok: [remote_target] => (item=site1.local) => {
    "msg": [
        "================ WEBSITE STATUS REPORT ================",
        "URL: http://site1.local",
        "HTTP Status: 200",
        "Content Verification: Page contains 'Welcome to VirtualHost: site1.local'",
        "======================================================="
    ]
}
ok: [remote_target] => (item=site2.local) => {
    "msg": [
        "================ WEBSITE STATUS REPORT ================",
        "URL: http://site2.local",
        "HTTP Status: 200",
        "Content Verification: Page contains 'Welcome to VirtualHost: site2.local'",
        "======================================================="
    ]
}

PLAY RECAP *********************************************************************************************************************************************
remote_target              : ok=11   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```


### Post-deployment local check

```bash
# Verify the first virtual host configuration
curl -H "Host: site1.local" http://127.0.0.1

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Welcome to site1.local</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; background-color: #f4f4f9; }
        .container { background: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        h1 { color: #333; }
        span { font-weight: bold; color: #0066cc; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to VirtualHost: site1.local</h1>
        <p>This page is dynamically generated by Ansible.</p>
        <ul>
            <li><strong>Managed Host Name:</strong> <span>sa-7</span></li>
            <li><strong>Managed Host FQDN:</strong> <span>sa-7.it-academy.local</span></li>
        </ul>
    </div>
</body>
</html>


# Verify the second virtual host configuration
curl -H "Host: site2.local" http://127.0.0.1

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Welcome to site2.local</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; background-color: #f4f4f9; }
        .container { background: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        h1 { color: #333; }
        span { font-weight: bold; color: #0066cc; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to VirtualHost: site2.local</h1>
        <p>This page is dynamically generated by Ansible.</p>
        <ul>
            <li><strong>Managed Host Name:</strong> <span>sa-7</span></li>
            <li><strong>Managed Host FQDN:</strong> <span>sa-7.it-academy.local</span></li>
        </ul>
    </div>
</body>
</html>

```
