so, it's a Linux manager, that can manage a range of thins from services to mounted devices and system states.

- Unit: a resource that the system is aware of and can control ex- service, socket, mount-point etc
- Unit File: Configuration file for managing the Unit
	- `.service`: service unit files
	- `.socket`: socket unit files
	- `.mount`: mount points unit files

#### Unit File Structure
Divided in several sections enclosed by [] brackets. 
```yaml
# Basic structure of a unit file structure
[Section]
Directive1=Value1
Directive2=Value2
```

We will go over a service `systemd unit file` example and see the most important sections and directives.

###### Important sections, directives and values
- `[Unit]`: contains metadata and relationships with other units.
	- `Description`: name of basic functionality or anything short and specific
	- `After`: Current service is started after the mentioned service, ex- `network.target`
- `[Service]`: Configuration of the service specific
	- `Type`: categorising the service by their behaviour:
		- `simple`: if you don't have any specific needs, this would suffice
		- `oneshot`: quick, short-lived and others should not wait for it
	- `ExecStart`: full path of arguments of the command needed to start the process, ex- `/usr/bin/python3 /ext/myscript.py`
- `[Install]`: Used to mark the behaviour of a unit if it's enabled or disabled
	- `WantedBy`: used to insert a dependency. Since `systemd` only executes those `systemd` unit that are someway dependent on some already running process
		- `multi-user.target`: this value will ensure that the current `systemd` unit will run

[Best resource to learn more](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)

Here's a example of a `systemd` service unit file that runs a script `/etc/boxreg/registration.py` on the condition if path `/etc/boxreg/box.env` exists. It is a one time process both standard output and error goes to journal and can be viewed through `journalctl` command
```yaml
# boxreg.service
Description=Box Registration Service
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /etc/boxreg/registration.py
ConditionPathExists=!/etc/boxreg/box.env
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```
This file would have to placed in `/etc/systemd/system/boxreg.service` and have to enabled by following commands
```bash
sudo systemctl daemon-reload # reload systemd to pick up new service
sudo systemctl enable boxreg.service # enable the service to start at boot up
sudo systemctl start boxreg.service # start the service immediately
```

`RWa4sWCp+Ji6++cQiDfa0DBdHgr6NC1TliJD3aeHGcA=`
key for the decryption