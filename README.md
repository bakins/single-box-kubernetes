# Single Server Kubernetes

This is a simple [Vagrant](http://vagrantup.com) configuration to initialize an "all-in-one" [Kubernetes](http://kubernetes.io) install on a single CoreOS virtual machine.

It uses a simple shell script to deploy and configure Kubernetes so as to not hide any of the details needed to get a single node functional.

## Status

This is a work in progress.  It has been developed and tested on Vagrant 1.7.2 with [Virtualbox](https://www.virtualbox.org) on Mac OS X Mavericks.

## Prep

You need Vagrant and at least one provider installed.

You also need the `kubectl` command. You can download it from https://github.com/GoogleCloudPlatform/kubernetes/releases - it is included in the release tarball.

## General Usage

Normal usage is to clone this repository, then run `vagrant up` inside it.  This will bring up a single CoreOS virtual machine and install and configure Kubernetes.

By default, kubernetes is listening on port 8080 of the virtual maching with no authentication needed.

If the provisioning fails, but the bot booted, then run `vagrant provision` and it should retry.

After the box is provisioned, you can configure kubectl to use it by running:

```
kubectl config set-cluster coreos --insecure-skip-tls-verify=true --server=http://172.17.8.100:8080
kubectl config set-context coreos --cluster=coreos
kubectl config use-context coreos
kubectl get nodes
```

You should see the node with its IP as the name.

## Customization

You can create a file named `config.rb` to override the variables set in `Vagrantfile`.

## Routing

You can route to your pods by adding a route on OS X: `sudo route add 10.1.0.0/16 172.17.8.100` - assuming default options in `Vagrantfile`

To route to the services, do: `sudo route add 10.200.0.0/16 172.17.8.100`

This assumes your host can talk directly to the virtual machine, which is the default.

## TODO

- https
- User authentication.
- name for kubelet node?

## Acknowlegements

- https://github.com/kelseyhightower/kubestack


