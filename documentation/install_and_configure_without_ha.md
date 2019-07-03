# Install and configure Extra Large without HA

Please see the [basic_usage.md](basic_usage.md) document for reference; this document will avoid repeating the information covered there.

This implementation currently includes Task Plans to install, configure, and later upgrade a new Extra Large stack without HA.

The current versions of those plans can be found at:

* [install_without_ha.pp](https://github.com/billclaytor/reidmv-pe_xl/blob/SLV-365/plans/install_without_ha.pp)
* [configure_without_ha.pp](https://github.com/billclaytor/reidmv-pe_xl/blob/SLV-365/plans/configure_without_ha.pp)
* [upgrade_without_ha.pp](https://github.com/billclaytor/reidmv-pe_xl/blob/SLV-365/plans/upgrade_without_ha.pp)

(TODO: update the above links if this update is merged)

## Basic usage instructions

1. Ensure the hostname of each system is set correctly, to the same value that will be used to connect to the system, and refer to the system as. If the hostname is not set as expected the installation plan will refuse to continue.
2. Install Bolt on a jumphost. This can be the master, or any other system.
3. Download or git clone the pe\_xl module and put it somewhere on the jumphost, e.g. ~/modules/pe\_xl.
4. Create an inventory file with connection information. An example is included below.
5. Create a parameters file. An example is included below. Note the addition of the "ha" parameter with a value of "false", and the omission of the "replica" roles.
6. Run the pe\_xl plan with the inputs created. Example:
```
        bolt plan run pe_xl \
          --inventory nodes.yaml \
          --modulepath ~/modules \
          --params @params.json 
```

Example nodes.yaml Bolt inventory file:

```yaml

---
groups:
  - name: pe_xl_nodes
    config:
      transport: ssh
      ssh:
        host-key-check: false
        user: centos
        run-as: root
        tty: true
    nodes:
      - pe-xl-core-0.lab1.puppet.vm
      - pe-xl-core-1.lab1.puppet.vm
      - pe-xl-compiler-0.lab1.puppet.vm
      - pe-xl-compiler-1.lab1.puppet.vm
```

Example params.json Bolt parameters file:

```json
{
  "install": true,
  "configure": true,
  "upgrade": false,
  "ha": false,

  "master_host": "pe-xl-core-0.lab1.puppet.vm",
  "puppetdb_database_host": "pe-xl-core-1.lab1.puppet.vm",
  "compiler_hosts": [
    "pe-xl-compiler-0.lab1.puppet.vm",
    "pe-xl-compiler-1.lab1.puppet.vm"
  ],

  "console_password": "puppetlabs",
  "dns_alt_names": [ "puppet", "puppet.lab1.puppet.vm" ],
  "compiler_pool_address": "puppet.lab1.puppet.vm",
  "version": "2019.1.0"
}
```
