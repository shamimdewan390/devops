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
## file transfer local pc to multipass
```
multipass transfer ~/Downloads/users.sql foo:/home/ubuntu/users.sql
```


```
sudo apt install apache2 -y
```
```
systemctl status apache2
```

server er kon port k lecen kortece ta janar jonno ai toos isntall korte hoe
```
sudo apt install net-tools
```
server er kon port k lecen kortece ta janar jonno
```
netstat -tulpn
```
ip janar jonno
```
ip r
```

sudo echo "hello world" > index.html
-bash: index.html: Permission denied
This command pipes the output to tee, which is run with sudo and can write the file as root
```
echo "hello world" | sudo tee index.html
```
or This runs the entire command, including the redirection, with root privileges
```
sudo bash -c 'echo "hello world" > index.html'
```

online theke download korar jonno "wget" use korte hoe
```
wget https://www.free-css.com/assets/files/free-css-templates/download/page296/browny.zip
```

```
sudo cp -R * /var/www/html/
```
or 
```
sudo cp folder -R  /var/www/
```

cp: for copy file or direcotry -R: cp to copy directories and their contents, including all subdirectories and files *: A wildcard that matches all files and directories in the current directory (except hidden files and directories, which start with a dot)
Be careful: If files with the same name exist in /var/www/html/, they will be overwritten without warning


























































