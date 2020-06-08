# What is this?

This repository is to serve as an example of how to compile a custom plugin for terraform in order to use inside your terraform configuration. There is a code example where you can see how to custom plugin is used.

# Why use it?

The custom providers are used to server a functionality that is not supported in other providers supported by HashiCorp.
There is no way to download the plugins using Terrafrom so the user has to compile them suited to the used OS. I will show an example and instruction on how to compile the provider for linux_amd64 architecture.

# How to use it?

### Prerequisites

- You must have vagrant installed.
Download from [here](https://www.vagrantup.com/downloads)

- You must have virtualbox installed.
Download from [here](https://www.virtualbox.org/wiki/Downloads)

- The custom provider we will use is able to work with Terraform v0.11.14 and go v1.10 using different version may be not compatible.

*Keep in mind the provided instruction is meant to be applied on MacOS. It could vary for different OS types*

### Step-by-step guide

1. Download this repo locally

`git clone git@github.com:yordanivh/custom_provider.git`

2. Bring a VM up using `vagrant`. VM will be created and provisioned usign the scripts in `scripts/` folder

`vagrant up`

3. Log in to the VM

`vagrant ssh`

4. Make sure that `go` folder in `/home/vagrant` has ownership set to `vagrant:vagrant`

`chown -R vagrant:vagrant ~/go`

5. Clone the custom provider to the go directory

`go get github.com/petems/terraform-provider-extip`

6. Change to the directory and compile the plugin

`cd ~/go/src/github.com/petems/terraform-provider-extip; make build`

7. After compile you can find the binary in `~/go/bin`

`ls -la ~/go/bin`

8. Create a project folder and copy the plugin to the `terraform.d` folder

```
mkdir -p /vagrant/terraform_project/terraform.d/plugins/linux_amd64
cp ~/go/bin/terraform-provider-extip /vagrant/terraform_project/terraform.d/plugins/linux_amd64/
```

9. Go to project folder to test the plugin

`cd /vagrant/terraform_project`

```hcl
cat main.tf

data "extip" "external_ip" {}

output "external_ip" {
  value = "${data.extip.external_ip.ipaddress}"
}
```

*terraform init*

```
vagrant@vagrant:/vagrant/terraform_project$ terraform init

Initializing provider plugins...

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

*terraform apply*

```
vagrant@vagrant:/vagrant/terraform_project$ terraform apply
data.extip.external_ip: Refreshing state...

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

external_ip = 93.XXX.XXX.XXX
```



