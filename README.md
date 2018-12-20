# packer_xenial64_dual
## Description:
Downloading the content of this repo, you will have all needed configuration files required to build  **two vagrant boxes:** 
- xenial64 virtualbox provider
- xenial64 vmware_desktop provider


## Files:
- `http/preseed.cfg` - file containing base configuration during the installation process
- `scripts` directory content:
    - `packages.sh` - bash script for configuration of main linux environment of the boxes
    - `vagrant.sh` - bash script which purpose is to create vagrant user and all related setup
    - `virtualbox.sh` - bash script related to virtualbox provider
    - `virtualbox_cleanup.sh` - bash script which removes all unnecessary stuff after `virtualbox.sh` and create some free space
    - `vmware.sh` - bash script related to vmware provider
    - `vmware_cleanup.sh` - bash script which removes all unnecessary stuff after `vmware.sh` and create some free space
- `xenial64.json` - file which **Packer** use in order to create our boxes
- `Gemfile` - Specify the the ruby version, and all gems needed for **Kitchen** test
- `.kitchen.yml` - **Kitchen** configuration file
- `test/integration/default/check_pkg.rb` - Script needed to **Kitchen** in order to test whether all needed packages are installed on your boxes. 
- `test/integration/default/check_pkg.rb` - script - helper used to create the test based on a list of packages

## Requiered software:
In order to build your box you need to have **Packer** tool installed.

Durring the building process you will need  **Virtualbox** and **VMware Workstation** tool installed.

In order to use the already created box you need **Vagrant** tool installed.

Please find Install section below in order to find out how to install **Virtualbox**, **Packer** and **Vagrant**.



## Install Section:
**Note that following instructions have been tested in Ubuntu**

### Instructions HOW to install `VMware Workstation`
- Go to [VMware Workstation downloads](https://my.vmware.com/web/vmware/details?downloadGroup=WKST-1502-LX&productId=799&rPId=28902) and click on Download Now
- Make the downloaded bundle file executable: `sudo chmod +x ~/Downloads/VMware-Workstation-Full-15*.bundle`
- Execute the bundle: `sudo ~/Downloads/VMware-Workstation-Full-15*.bundle`

### Instructions HOW to install `Virtualbox`
- Go to [Virtualbox downloads](https://www.virtualbox.org/wiki/Linux_Downloads) choose **Virtualbox** package
- Type in your terminal: `sudo apt-get install -y virtualbox `

### Instructions HOW to install `Packer`
- Download **Packer** from [here](https://www.packer.io/)
- Click on following link: [Install Packer](https://www.packer.io/intro/getting-started/install.html) 

### Instructions HOW to install `Vagrant`
- Download **Vagrant** from [here](https://www.vagrantup.com/downloads.html)
- Click on following link: [Installing vagrant](https://www.vagrantup.com/docs/installation/)

### Instructions HOW to install `Vagrant VMware Utility`
- Go to [Vagrant VMware Utility page](https://www.vagrantup.com/vmware/downloads.html) and choose the proper package for your system
- Follow the install instructions for [Vagrant VMware Utility](https://www.vagrantup.com/docs/vmware/installation.html)

### Instructions HOW to build the nginx64 box
- Make sure you have at least 4GB free space on your drive before start building of the boxes.
- Download the content of **berchev/packer_xenial64_dual** repository: `git clone https://github.com/berchev/packer_xenial64_dual.git`
- Change to downloaded **packer_xenial64_dual** directory: `cd packer_xenial64_dual`
- Run command: `packer build xenial64.json` and wait the script to finish
- Once the script finish type: `ls` and you will see the newly generated files:
  - xenial64-vbox.box
  - xenial64-vmware.box


### Instructions HOW to use the already created vagrant boxes

You have two options here:
- Option A - add and use the created boxes locally
- Option B - add created boxes to Vagrant cloud and then use it directly from the cloud.

Note that **Option B** have one serious advantage - you can access your boxes from everywhere! On everyone computer with access to Internet.

### Option A - Add and use both boxes locally
- On your terminal type: `vagrant box add --name <box_name> <desired_box>` , where `<box_name>` is the name which you pick for your box. For example: **xenial64-vbox.box** and **xenial64-vmware.box** (it is good approach to use lowercase letters in Linux)
- On your terminal type: `vagrant init <box_name>`, where **<box_name>** is the name which we picked in step 1. 
It can be any other name!

`vagrant init <box_name>` command will place to the current directory `Vagrantfile`. 

**Note that you can have only one Vagrantfile into the current working directory.**

- Type: `vagrant up` in order to power on your box (If you want to power on box with virtualbox provider you need to specify **provider** - `vagrant up -- provider virtualbox`. Vagrant works by default with **vmware_desktop** provider!)
- Type: `vagrant ssh` and you will be connected to your box.
- Type: `exit ` in order to logout
- Type: `vagrant halt` in order to poweroff the box
- Type: `vagrant destroy` in order to destroy the created box

### Option B - Add and use both boxes from Vagrant cloud
- Create account in the [Vagrant cloud](https://app.vagrantup.com/)
- Click on **New Vagrant Box**
- Fill in **Name** field
- Fill in **Version** and **Description**
- Add **Provider** (for example: **vmware_desktop**) and click **continue to upload**
- Choose the vmware box(xenial64-vmware.box)
- Once uploaded click on **add provider** and choose **virtualbox**
- Click on **continue to upload** and choose your virtualbox box(xenial64-vbox.box)
- Once completed **release version** and your boxes are ready for download
- Go to your personal computer terminal and type: `vagrant init -m <your_vagrant_cloud_user>/<name_of_your_box_in_vagrant_cloud>`
- Type: `vagrant up` in order to power on your box. (If you want to power on box with virtualbox provider you need to specify **provider** - `vagrant up -- provider virtualbox`. Vagrant works by default with **vmware_desktop** provider!)
- Type: `vagrant ssh` and you will be connected to your box.
- Type: `exit ` in order to logout
- Type: `vagrant halt` in order to poweroff the box
- Type: `vagrant destroy` in order to destroy the created box

### Prepare your environment for **kitchen**
- Type: `sudo apt-get install rbenv ruby-dev ruby-bundler`
- add to your ~/.bash_profile: 
  ```
  eval "$(rbenv init -)"
  true
  export PATH="$HOME/.rbenv/bin:$PATH"
  ```
- do `. ~/.bash_profile` in order to apply the changes made in .bash_profile 

- Change to the directory with `Gemfile` and type: `bundle install` in order to install all needed gems for the test

### Test your boxes with **kitchen** after creation:
- Edit `.kitchen.yml` according to your needs.
Note that if your boxes names added to vagrant are `xenial64_vbox` and `xenial64-vmware`, you do not need to change anything!
- Type: `bundle exec kitchen list` to list the environment
- Type: `bundle exec kitchen converge` to build environment with kitchen
- Type: `bundle exec kitchen verify` to test the created kitchen environment
- Type: `bundle exec kitchen destroy` in order to destroy the created kitchen environment
- Type: `bundle exec kitchen test` in order to do steps from 3 to 5 in one command

### Kitchen test output

The output from bundle exec kitchen verify command should be similar to this one:
```
Profile: tests from {:path=>"/home/gberchev/spreadsheet/packer/xenial64_dual_provider/packer_xenial64_dual/test/integration/default"} (tests from {:path=>".home.gberchev.spreadsheet.packer.xenial64_dual_provider.packer_xenial64_dual.test.integration.default"})
Version: (not specified)
Target:  ssh://vagrant@127.0.0.1:2200

  System Package thin-provisioning-tools
     ✔  should be installed
  System Package python-pip
     ✔  should be installed
  System Package python3-pip
     ✔  should be installed
  System Package git
     ✔  should be installed
  System Package jq
     ✔  should be installed
  System Package curl
     ✔  should be installed
  System Package wget
     ✔  should be installed
  System Package vim
     ✔  should be installed
  System Package language-pack-en
     ✔  should be installed
  System Package sysstat
     ✔  should be installed
  System Package htop
     ✔  should be installed
  System Package ruby
     ✔  should be installed
  System Package ruby-dev
     ✔  should be installed

Test Summary: 13 successful, 0 failures, 0 skipped
       Finished verifying <default-xenial64-vbox> (0m1.69s).
-----> Setting up <default-xenial64-vmware>...
       Finished setting up <default-xenial64-vmware> (0m0.00s).
-----> Verifying <default-xenial64-vmware>...
       Loaded tests from {:path=>".home.gberchev.spreadsheet.packer.xenial64_dual_provider.packer_xenial64_dual.test.integration.default"} 

Profile: tests from {:path=>"/home/gberchev/spreadsheet/packer/xenial64_dual_provider/packer_xenial64_dual/test/integration/default"} (tests from {:path=>".home.gberchev.spreadsheet.packer.xenial64_dual_provider.packer_xenial64_dual.test.integration.default"})
Version: (not specified)
Target:  ssh://vagrant@127.0.0.1:2222

  System Package thin-provisioning-tools
     ✔  should be installed
  System Package python-pip
     ✔  should be installed
  System Package python3-pip
     ✔  should be installed
  System Package git
     ✔  should be installed
  System Package jq
     ✔  should be installed
  System Package curl
     ✔  should be installed
  System Package wget
     ✔  should be installed
  System Package vim
     ✔  should be installed
  System Package language-pack-en
     ✔  should be installed
  System Package sysstat
     ✔  should be installed
  System Package htop
     ✔  should be installed
  System Package ruby
     ✔  should be installed
  System Package ruby-dev
     ✔  should be installed

Test Summary: 13 successful, 0 failures, 0 skipped
       Finished verifying <default-xenial64-vmware> (0m1.90s).
-----> Kitchen is finished. (0m4.94s)
```

## TODO

