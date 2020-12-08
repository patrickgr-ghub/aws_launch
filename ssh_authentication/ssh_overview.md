## Introduction to SSH Authentication

<p>For those that are newer to Cloud Database Management, the concept of SSH may be unfamiliar. This document reviews the purpose and application of the basic concepts of SSH Authentication.</p>

#### What is SSH?

<p>As defined on Wikipedia:

SSH or Secure Shell is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH.

SSH provides a secure channel over an unsecured network by using a client–server architecture, connecting an SSH client application with an SSH server. The protocol specification distinguishes between two major versions, referred to as SSH-1 and SSH-2. The standard TCP port for SSH is 22. SSH is generally used to access Unix-like operating systems, but it can also be used on Microsoft Windows. Windows 10 uses OpenSSH as its default SSH client and SSH server.
</p>

[Click here to access the full Wikipedia Overview](https://en.wikipedia.org/wiki/SSH_(Secure_Shell))

#### SSH in simple terms

<p>SSH allows users to make encrypted command line calls to servers using authentication credentials that are stored within key pair.

AWS describes it this way: "After you launch your instance, you can connect to it and use it the way that you'd use a computer sitting in front of you."</p>

[Click here for the full AWS Article](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

<p>It is often necessary to interface with database instances leveraging the terminal to load files, test scripts, update servers, and perform many other actions. SSH makes it possible to establish a secure connection to systems so that users can make requests from their local machine or from other machines outside of the core network.</p>

#### Establishing up your SSH Credentials

<p>Prior to using SSH, there are a couple of key steps that need to be taken to establish a secure identity that will be used to make calls to your remote machines. The first and most important is establishing a key pair:
  
AWS documentation describes a key pair this way: "A key pair, consisting of a private key and a public key, is a set of security credentials that you use to prove your identity when connecting to an instance. Amazon EC2 stores the public key, and you store the private key. You use the private key, instead of a password, to securely access your instances. Anyone who possesses your private keys can connect to your instances, so it's important that you store your private keys in a secure place."
  
To create a key pair, you need to log into AWS and follow these steps:

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

2. In the navigation pane, under NETWORK & SECURITY, choose Key Pairs.

3. Choose Create key pair.

4. For Name, enter a descriptive name for the key pair. Amazon EC2 associates the public key with the name that you specify as the key name. A key name can include up to 255 ASCII characters. It can’t include leading or trailing spaces.

5. For File format, choose the format in which to save the private key. To save the private key in a format that can be used with OpenSSH, choose pem. To save the private key in a format that can be used with PuTTY, choose ppk.

6. Choose Create key pair.

7. The private key file is automatically downloaded by your browser. The base file name is the name you specified as the name of your key pair, and the file name extension is determined by the file format you chose. Save the private key file in a safe place.

> !! Important: This is the only chance for you to save the private key file. !!

8. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it.

> chmod 400 my-key-pair.pem

If you do not set these permissions, then you cannot connect to your instance using this key pair. For more information, see Error: Unprotected private key file.

</p>

#### Connecting to an instance through SSH

#### Uploading files to an instance from terminal








This file will sample some of the concepts of ssh and how to authenticate to servers using terminal.
