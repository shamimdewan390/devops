# Install workstation 

## vmware

free and paid both Heavier, designed for robust workloads, GUI and CLI, more features, higher learning curve, Advanced networking, snapshots, cloning, VM migration, nested virtualization, enterprise tools, Paid (Workstation/Fusion), free (ESXi basic), enterprise licensing

[vmware official]([https://canonical.com/multipass/install](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion))


## multipass 

totally free Lightweight, minimal overhead,	Simple CLI, easy to launch and manage VMs, mostly Ubuntu, basic resource settings, Integrates with cloud-init, some VM managers, and CI/CD pipelines,  Linux images with custom config

[muptipass official](https://canonical.com/multipass/install)

Launch an instance (by default you get the current Ubuntu LTS):
```
multipass launch --name foo
```

```
multipass exec foo -- lsb_release -a
```
```
multipass list
```
```
multipass stop foo bar
```
```
multipass start foo
```
```
multipass delete bar
```































