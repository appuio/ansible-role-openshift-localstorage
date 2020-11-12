# OpenShift local storage setup

Configure local volumes on OpenShift hosts and make them available to
containers as `hostPath` persistent storage objects.


## Variables

* `appuio_openshift_localstorage`: Dictionary mapping from volume name to
  settings:

  * `lvm_vg`: Logical Volume Manager (LVM) volume group name, i.e. `vgssd`
  * `size`: Size with unit, i.e. `1G`
  * `mountopts`: Mount options, see `mount(8)`
  * `pv_create`: Create Persistent Volume object in OpenShift
  * `pv_labels`: Dictionary with additional labels for PV object
  * `pv_annotations`: Dictionary with additional annotations for PV object
  * `pv_storage_class`: Storage class name
  * `pv_reclaim_policy`: Reclaim policy

  Additionally permissions for the mountpoint directory can be specified. See
  [Ansible file module](https://docs.ansible.com/ansible/latest/file_module.html)
  for their description. Supported parameters:
  `owner`, `group`, `mode`, `seuser`, `serole`, `setype` and `selevel`.

* `appuio_openshift_localstorage_defaults`: Default values for volume settings.

* `appuio_openshift_localstorage_override`: Override values for all volumes.

* `appuio_openshift_localstorage_openshift_node_name`: OpenShift node name.
  Defaults to `inventory_hostname`. Must be set to the same value as
  `openshift_kubelet_name_override`. For AWS environments, this usually is
  `ec2_private_dns_name`.

## Example playbook

```
- hosts: node.example.com
  gather_facts: false
  roles:
  - ansible-role-openshift-pv
  vars:
    appuio_openshift_localstorage_defaults:
      lvm_vg: vgssd
      size: 120M
      mode: '0750'
    appuio_openshift_localstorage:
      es1: {}
      test1:
        size: 100M
        owner: nobody
        setype: unlabeled_t
        pv_labels:
          purpose: "Testing"
```


## Dependencies

* <https://github.com/appuio/ansible-module-openshift>
