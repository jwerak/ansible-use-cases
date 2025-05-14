# Notes

Install ansible-builder

```bash
 dnf install --enablerepo=ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms ansible-builder
```

Build

```bash
ansible-builder build --build-arg ANSIBLE_GALAXY_SERVER_AHUB_CERTIFIED_TOKEN --build-arg ANSIBLE_GALAXY_SERVER_AHUB_VALIDATED_TOKEN -v3 --tag quay.io/jveverka/ansible-official-ee-aap
```
