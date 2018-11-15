Command + Shift + . -> 显示隐藏文件
————————————————————————————————————————————————————————————————————————————

使用Filezilla to Transfer and Manage Files Securely on your VPS，在mac上的配置资源如下：
1. https://www.digitalocean.com/community/tutorials/how-to-use-filezilla-to-transfer-and-manage-files-securely-on-your-vps

2. https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2

3. https://filezilla-project.org/download.php?type=client

具体步骤：
When needing to upload or download files from your VPS in real time, you essentially have the following options:

File Transfer Protocol (FTP);
Secure Copy Program (SCP);
SSH File Transfer Protocol (SFTP); or
FTP over TLS/SSL (FTPS).
FTP *not secure
Among the various file-transfer options, one should never, ever, ever connect to a remote server via FTP; SCP and SFTP are just as easy to use, but provide much more security. In addition, while FTP requires the installation of FTP server software such as vsFTP or ProFTP, both SCP and SFTP utilize the SSH protocol and, as a result, will work "out-of-the-box" when connecting to a remote Unix-like machine, such as Mac OS X or Linux.

SCP vs. SFTP
Given that both SCP and SFTP utilize the SSH protocol in connecting to another computer, the two methods are fairly equal in regard to security. SFTP has a slight edge in regard to efficiency, because an interrupted file-transfer can resume where it left off in the event of a broken connection that is later re-established.

SFTP vs. FTPS
SFTP should not be confused with FTPS, because the two methods are incompatible with each other. While FTPS can provide equal security, it does require additional steps to deploy if one does not already have an SSL certificate.

SFTP Clients
There are several quality SFTP clients out there: Cyberduck, Filezilla or WinSCP, to name a few. This article, however, will focus on Filezilla – an open-source (i.e. free) FTP client for Windows, Mac OS X and Linux. In addition to being able to download the program, the filezilla-project.org site also contains a documentation Wiki and a Support Forum.

Key-based Authentication
With SFTP, you have two user-authentication options when connecting to a cloud server: (i) passwords or (ii) SSH keys. For a discussion on the benefits of SSH keys over passwords and/or instructions on setting up password-less logins on your server, please refer to How To Create SSH Keys with PuTTY to Connect to a VPS.

SFTP via SSH2 Key-based Authentication
FileZilla has a built-in key management page in the Settings dialog, which allows you to save your Public (SSH) Key and to (securely) automate the process of connecting to a remote server.

Prequisite
If you have yet to create an SSH key pair, you can do so by following one of two DigitalOcean tutorials:

Windows users: How To Create SSH Keys with PuTTY to Connect to a VPS
Mac OSX & Linux users: How To Set Up SSH Keys
Follow these steps once you have an SSH key pair that you would like to use to connect to your VPS:

Open the FileZilla client.
From the top of the home screen, click on Edit and select Settings.
On the left side of the menu, expand the Connection section and highlight SFTP.
FileZilla Key Manager

Click on the [Add keyfile...] button and browse your local machine's directories and select your Private Key file.
Then, again from the top of FileZilla's home screen, click on File and select Site Manager.
Finally, on the left side of the Site Manager, click on the New Site button and type a unique name under My Sites that will allow you to easily identify this particular remote server in the future.
FileZilla Site Manager

Now, under the General tab, fill in the Host (with either an IP address or FQDN) and Port fields (default is 22).
In the Protocol dropdown menu, select SFTP - SSH File Transfer Protocol.
In the Logon Type dropdown menu, select Interactive.
Note for PuTTY users with passphrase-protected public keys: If your original .ppk file is password-protected, FileZilla will convert your .ppk file to an unprotected one when importing the key into FileZilla. As of version 3.0.10, a password-protected key file is not yet supported.

If a password-protected key file is desired, FileZilla is able to utilize PuTTY's Pageant tool.

Simply run Pageant; in your system tray, you will see the Pageant icon appear.
Right-click on the icon and select Add Key and select your private key (.ppk) file.
Then, follow the prompt to enter your passphrase.
Finally, launch FileZilla and connect to your virtual private server via SFTP using SSH2 with a username and an empty password (do not forget to close pageant when you are done).


-------------------------
## set up ssh keys to connect with VPS
About SSH Keys
Secure Shell (better known as SSH) is a cryptographic network protocol which allows users to securely perform a number of network services over an unsecured network. SSH keys provide a more secure way of logging into a server with SSH than using a password alone. While a password can eventually be cracked with a brute force attack, SSH keys are nearly impossible to decipher by brute force alone.

Generating a key pair provides you with two long string of characters: a public and a private key. You can place the public key on any server, and then unlock it by connecting to it with a client that already has the private key. When the two match up, the system unlocks without the need for a password. You can increase security even more by protecting the private key with a passphrase.

Within some of the commands found in this tutorial, you will notice some highlighted values. These are variables, and you should substitute them with your own values.

Step One—Create the RSA Key Pair
The first step is to create the key pair on the client machine (there is a good chance that this will just be your computer):

ssh-keygen -t rsa
Step Two—Store the Keys and Passphrase
Once you have entered the Gen Key command, you will get a few more questions:

Enter file in which to save the key (/home/demo/.ssh/id_rsa):
You can press enter here, saving the file to the user home (in this case, my example user is called demo).

Enter passphrase (empty for no passphrase):
It's up to you whether you want to use a passphrase. Entering a passphrase does have its benefits: the security of a key, no matter how encrypted, still depends on the fact that it is not visible to anyone else. Should a passphrase-protected private key fall into an unauthorized users possession, they will be unable to log in to its associated accounts until they figure out the passphrase, buying the hacked user some extra time. The only downside, of course, to having a passphrase, is then having to type it in each time you use the key pair.

The entire key generation process looks like this:

ssh-keygen -t rsa
Output
Generating public/private rsa key pair.
Enter file in which to save the key (/home/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/demo/.ssh/id_rsa.
Your public key has been saved in /home/demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
The public key is now located in /home/demo/.ssh/id_rsa.pub. The private key (identification) is now located in /home/demo/.ssh/id_rsa.

Step Three—Copy the Public Key
Once the key pair is generated, it's time to place the public key on the server that we want to use.

You can copy the public key into the new machine's authorized_keys file with the ssh-copy-id command. Make sure to replace the example username and IP address below.

ssh-copy-id demo@198.51.100.0
Note: If you are a Mac user, ssh-copy-id will not be installed on your machine. You can, however, install it using Homebrew:

brew install ssh-copy-id
Alternatively, you can paste in the keys using SSH:

cat ~/.ssh/id_rsa.pub | ssh demo@198.51.100.0 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >>  ~/.ssh/authorized_keys"
No matter which command you chose, you may see something like:

The authenticity of host '198.51.100.0 (198.51.100.0)' can't be established.
RSA key fingerprint is b1:2d:33:67:ce:35:4d:5f:f3:a8:cd:c0:c4:48:86:12.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '198.51.100.0' (RSA) to the list of known hosts.
user@198.51.100.0's password: 
This message helps us to make sure that we haven't added extra keys that you weren't expecting.

Now you can go ahead and log into your user profile and you will not be prompted for a password. However, if you set a passphrase when creating your SSH key, you will be asked to enter the passphrase at that time (and whenever else you log in in the future).

Optional Step Four—Disable the Password for Root Login
Once you have copied your SSH keys onto your server and ensured that you can log in with the SSH keys alone, you can go ahead and restrict the root login to only be permitted via SSH keys.

In order to do this, open up the SSH config file:

sudo nano /etc/ssh/sshd_config
Within that file, find the line that includes PermitRootLogin and modify it to ensure that users can only connect with their SSH key:

/etc/ssh/sshd_config
PermitRootLogin without-password
Save and close the file when you are finished.



______________________________________
