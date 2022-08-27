---
title: Compiled Cardano-Node using CVM
author: Orelvis Lago
date: 2022-07-18 20:55:00 +0800
categories: [cvm]
tags: [cvm, stakepool, release]
pin: false
---
> After version 0.3.0 of CVM several changes were made that affect the commands, any questions visit this post [**Release Cardano Version Manager v0.3.0**](../CVM-v0.3.0-relases/).
{: .prompt-info }

## Starting

Hello again, this time I wanted to show you a tutorial on how to build your own cardano node using CVM.

### What we will cover in this tutorial

- Server configuration to run the cardano node
- Cardano Server Installation
- Version management

### That we will not cover

 - Server operating system installation
 - Server security
 - Post sync ledger setup

### Environment in which we will work

Virtual machine, 16gb Ram, 4 Cores, Ubuntu 22.04


### let's start: Installing CVM

To install cvm we just have to execute the following line in our terminal.

```console
curl https://raw.githubusercontent.com/orelvis15/cvm/master/install.sh -sSf | bash && source "$HOME"/.cvm/env
```

Once the execution is finished you should have an output like this

<img src="/assets/img/cvm/cvm_install.png" width="750" height="500" />

Let's run ***cvm help*** to verify that the installation was successful.

<img src="/assets/img/cvm/help.png" width="750" height="500" />

### Initial settings for using CVM

CVM creates the folder structure needed to start the cardano node in the /opt directory, we need to make sure that the current user has write permissions to this directory.

We add the current user in the **sudo** group.

```console
sudo adduser [user] sudo
```

Replace [user] with the user, in our case it would be **sudo adduser orelvis sudo**.

We add the sudo group as owner of the /opt directory

```console
sudo chown -R root:sudo /opt
```

We assign read/write permissions to the user and group owners of the /opt directory

```console
sudo chmod -R 775 /opt
```

We run ls -la and if all went well we should have an output like this.

<img src="/assets/img/cvm/ls_opt.png" width="400" height="350" />


### Preparing the server with CVM

To be able to use a cardano node we need to install certain dependencies and download several configuration files, we will be able to do all this with the **cvm init** command

```console
cvm init
```

This command will do the following.
  - Install all the necessary dependencies to run and compile the cardano node.
  - Create the necessary folder structure in the /opt directory
  - Download the configuration files published by IOK.
  - Download the scripts published by the [guild-operator](https://github.com/cardano-community/guild-operators) community to manage our node.

If all went well you should see an output like this.

<img src="/assets/img/cvm/init.png" width="600" height="450" />


### Compiling Cardano node

The safest way to use cardano node binaries is if we compile it ourselves, there is the option recommended by the community, that's why this functionality is built into CVM.

In the last section we prepared our server to compile cardano node without problems.

We run the command **cvm install x.x.x** where **x.x.x** is the cardano version we want to install, if we only want to install the latest version we can run **cvm install** and cvm will find the latest version version available.

```console
cvm install
```

This command will perform the following actions.
- Clone the cardano node repository with the latest changes.
- Makes sure that it is in the tag of the version that was passed by parameters.
- Update Cabal packages.
- Compile the cardano node.
- Create a folder in /opt/cardano/bin with the name of the version that was installed and copy the generated binaries into it.


Once finished you should have something like this.

<img src="/assets/img/cvm/install.png" width="600" height="450" />


If we now execute ***cvm ls** we will see that we have an output similar to this.

```console
cvm ls
```

<img src="/assets/img/cvm/list.png" width="350" height="250" />

### Starting the node

We already have node installed on our server, now we need to get it up and running.

first we are going to run **cvm use x.x.x**.

```console
cvm use 1.35.0
```

This command will do the following:
- Save as the version of cardano to execute the pass by parameters.
- In case the cardano service does not exist, it will be created
- It will restart the systemctl daemon to get the changes in the service

***This step requires administrator access, it is common to ask for the root password***

You should have an output like this

<img src="/assets/img/cvm/use.png" width="400" height="250" />


If we now execute **cvm list** we should get an output similar to this.

<img src="/assets/img/cvm/list_use.png" width="300" height="250" />

Verifiquemos que el sistema está reconociendo el nodo de cardano y la cardano cli.

```console
cardano-node --version
```

<img src="/assets/img/cvm/cardano_node.png" width="400" height="250" />

```console
cardano-clie --version
```

<img src="/assets/img/cvm/cardano_cli.png" width="400" height="250" />

Now everything is ready to start with the synchronization of the node.

We start the cardano node service

```console
cvm start
```

<img src="/assets/img/cvm/cvm_start.png" width="300" height="250" />

We check that the service is running

```console
systemctl cnode status
```

<img src="/assets/img/cvm/cnode_status.png" width="600" height="600" />

### Monitoring synchronization

We can keep track of the synchronization with the ledger using the guild-operator community scripts

We access the scripts directory in /opt/cardano.

```console
cd /opt/cardano/scripts
```

2 - Now we run **gLiveView**

```console
./gLiveView.sh
```

You should see something like this

<img src="/assets/img/cardano_node/sync_status.png" width="600" height="600" />