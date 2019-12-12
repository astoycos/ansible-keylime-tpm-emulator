Ansible Keylime
===============

[![Build Status](https://travis-ci.org/keylime/ansible-keylime-tpm-emulator.svg?branch=master)](https://travis-ci.org/keylime/ansible-keylime-tpm-emulator) [![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/keylime-project/community)

Ansible role to deploy [Keylime](https://github.com/keylime/keylime) with a
pre-configured and ready to use TPM Emulator.

For details on using Keylime, please consult the
[project documentation](http://keylime-docs.rtfd.io/)

Security Warning
-----------------
Do not use a software TPM emulator in a production environment.

SELinux is set to permissive for this role.

This role is designed to enable development environment provisioning or to
a sandbox environment for anyone who would like to test drive Keylime.

Should you want to deploy with a hardware TPM, use the [anisble-keylime role](https://github.com/keylime/ansible-keylime)

Usage
-----

Run the example playbook against your target remote node(s).

```
ansible-playbook -i your_hosts playbook.yml
```

TPM Version Control
-------------------

Either TPM version 1.2 or TPM 2.0 support can be configured by simply changing
the role in the `playbook.yml` file [here](https://github.com/keylime/ansible-keylime-tpm-emulator/blob/master/playbook.yml#L14).

For TPM 2.0 use:

```
  - ansible-keylime-tpm20
```

For TPM 1.2 use:

```
  - ansible-keylime-tpm12
```

Both roles will deploy the relevant TPM 1.2 Emulator (tpm4720) or 2.0 Emulator
(IBM software TPM).

Vagrant
-------

A `Vagrantfile` is available for provisioning.

Clone the repository and then simply run with the following additional args
added to the `vagrant` command:


* `--instances`: The number of Keylime Virtual Machines to create. If not provided, it defaults to `1`
* `--repo`: This mounts your local Keylime git repository into the virtual machine (allowing you to test your code within the VM). This is optional.
* `--cpus`: The amount of CPU's. If not provided, it defaults to `2`
* `--memory`: The amount of memory to assign.  If not provided, it defaults to `2048`

For example, using libvirt:

```
vagrant --instances=2 --repo=/home/jdoe/keylime --cpus=4 --memory=4096  up --provider libvirt --provision
```

For example, using VirtualBox:

```
vagrant --instances=2 --repo=/home/jdoe/keylime --cpus=4 --memory=4096  up --provider virtualbox --provision
```

| NOTE: Customized args (`--instances`, `--repos` etc), come before the mainvagrant args (such as `up`, `--provider`) |
| --- |

Connect to the two VMs by bringing up two terminals already in the `keylime_environment_setup` directory 

Then run `vagrant --instances=2 ssh keylime1` in the first terminal and `vagrant --instances=2 ssh keylime2` in the second terminal

Navigate to the root directory in both VMs with the command `sudo su -`

Ensure keylime shared folder is working with `cd keylime_master` and then run `python3 setup.py install` to ensure the keylime codebase is loaded properly

Then run `tpm2_getrandom 8 in each VM` to ensure the TPM emulator is running, the ouput should be similar to the following

`0x6F 0xA7 0xE0 0x28 0x98 0x33 0x62 0x78`

If function returns an error try to start the tpm server with `tpm_serverd` and try again

For each VM, open 3 more Terminals so that you have 4 terminals connected to keylime1 and 4 terminals connected to keylime2. 

Lastly, You can then start the various components using commands:

```
keylime_verifier

keylime_registrar

keylime_node
```
Note: To avoid reprovisioning your machines after use simple run `vagrant --instances=2 halt` when done with current session and `vagrant --instances=2 reload` to power the VM's back up 

WebApp
------

The web application can be started with the command `keylime_webapp`. If using
Vagrant, port 443 will be forwarded from the guest to port 8443 on the host.

This will result in the web application being available on url:

https://localhost:8443/webapp/


IMA Policy
----------

This role deploys a basic ima-policy into `/etc/ima/ima-policy` so that IMA
run time integrity may be used. For this to activate, you must reboot the
machine first (if you're using vagrant, perform `vagrant reload`)

Should you reboot the machine, you will need to start the emulator again:

`/usr/local/bin/tpm_serverd`

`systemctl restart tpm2-abrmd`

License
-------
Apache 2.0

Contribute
----------

Please do! Pull requests are welcome.

Please ensure CI tests pass!

Contributors
------------

* Luke Hinds (lhinds@redhat.com)
* Leo Jia (ljia@redhat.com )
* Andrew Stoycos (astoycos@bu.edu)
* Amy Pattanasethanon (raynecarnes@gmail.com)
