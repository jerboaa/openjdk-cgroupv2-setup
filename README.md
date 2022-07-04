# OpenJDK cgroup v2 setup
This repository contains a rudimentary ansible playbook for a basic cgroup v2 setup which then works for OpenJDK jtreg container tests.

Note: This has only been tested on Fedora Workstation (33-36) so far.

# Usage

Prerequisites:
- Fresh installed Fedora host (VM or physical)
- Configured ansible hosts

```
$ grep -A1 cgroup /etc/ansible/hosts 
[cgroup_test_hosts]
192.168.122.81
$ ansible cgroup_test_hosts -m ping
192.168.122.81 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

In order to run the cgroup v2 setup as playbook do:

```
$ git clone https://github.com/jerboaa/openjdk-cgroupv2-setup.git
$ cd openjdk-cgroupv2-setup.git
$ ansible-playbook --ask-become-pass config_cgroupv2.yml
```

Then, once you've done that prepare the JDK to test and run them. Example:

```
$ rm -rf JTwork/ JTreport && jtreg -e:PATH \
                               -timeout:4 -jdk:./jdk-build -verbose:summary \
                               -Djdk.test.container.command=podman \
                               -Djdk.test.docker.image.name=fedora \
                               -Djdk.test.docker.image.version=32 \
                               test/hotspot/jtreg/containers
```
