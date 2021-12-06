### Adding rps user to sudoers file from terminal

Edit /etc/sudoers and add rps user as shown below
```
su -
vim /etc/sudoers
```
You need to add the below line just below line no 100
<pre>
rps ALL=(ALL) ALL
</pre>

Also you need to uncommend line no 111 as shown below
<pre>
%wheel  ALL=(ALL) NOPASSWD: ALL
</pre>

### Installing git in CentOS 
```
sudo yum install -y git
```

