---
layout: default
title: Getting Set Up
---

# Getting Set Up

The instructor will give you the IP address and credentials that you will need to SSH into your DC/OS cluster.

### Step 1
Set Up DC/OS Command Line

If you have a Macbook or Linux laptop and you don't have any restrictions on accessing servers on the internet, you can use the instructions in Step 2 on your laptop. If you don't then you should login to your cluster's "bootstrap" server and use it as a command line client.

Download the id_rsa key from the workshop cluster Github location at:

https://github.com/tbaums/dcos-mandt-labs/tree/master/keys

If using Windows and Putty Telnet, you will need to download the key above and convert it to a `.ppk` file. Please see the instructor with questions.  

### Step 2

If your laptop can access any servers on the internet without restriction, please install the DC/OS CLI locally on your machine. If your laptop is restricted, please SSH into your "bootstrap" server and install the DC/OS CLI there. For the rest of the course, you will execute DC/OS command line commands from your bootstrap server (located within your VPC).

To SSH to the bootstrap server:
```
ssh -i ./id_rsa centos@<your bootstrap server ip address>
```

**All students continue here:**

Your instructor will give you:
- Master IP address
- Public IP address
- DC/OS user and password

Access your DC/OS Dashboard using the URL `https://<MASTER IP>/`.  Make sure you use *HTTPS*.

Set up the DC/OS command line by clicking on the top left and choosing "Install CLI"

![CLI](https://docs.mesosphere.com/1.12/img/install-cli.png)

Click in the dialogue box to copy the command

![Copy Command](https://docs.mesosphere.com/1.12/img/CLI-Installation-GUI_Popup_Linux-1.12.png)

Paste that command into your Terminal and press enter

For CoreOS use the following commands to install the CLI binary:

```
sudo mkdir -p /opt/bin &&
curl https://downloads.dcos.io/binaries/cli/linux/x86-64/dcos-1.12/dcos -o dcos &&
chmod +x dcos &&
sudo mv dcos /opt/bin
dcos cluster setup https://34.201.164.41
dcos
```

Using Homebrew
```
brew install dcos-cli
```


Once the CLI is installed, confirm that it is installed correctly and connected to your cluster by running following command

```
dcos node
```

The output should be a list of nodes in the cluster

```

   HOSTNAME        IP                         ID                     TYPE                 REGION          ZONE       
  10.0.0.101   10.0.0.101  94141db5-28df-4194-a1f2-4378214838a7-S0   agent            aws/us-west-2  aws/us-west-2a  
  10.0.2.100   10.0.2.100  94141db5-28df-4194-a1f2-4378214838a7-S4   agent            aws/us-west-2  aws/us-west-2a
```

### Step 4 Tour DC/OS Catalog

Your instructor will give you a tour of DC/OS UI and catalog.

All labs are available onine at [dcos-labs](https://github.com/dcos/demos)

You can find the source code for the labs at GitHub:
[dcos][dcos-organization] /
[demos](https://github.com/dcos/demos)

[dcos-organization]: https://github.com/dcos
