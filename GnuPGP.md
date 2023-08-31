# GnuPGP.md
## 添加ssh支持
查看现有证书
```
$ gpg --list-keys
/home/liuqi/.gnupg/pubring.kbx
------------------------------
pub   rsa3072 2023-08-30 [SC]
      BC9313A0F060B81897FA8FEE4C54AEBAFD434AEC
uid           [ultimate] Qi Liu
sub   rsa3072 2023-08-30 [E]
```
添加一个支持\[A\]功能(Authenticate)的证书；`addkey`进入功能选项，选择8自定义rsa功能，添加`A`后去除`S`和`E`，设置一年有效期，最后保存
```
$ gpg --expert --edit-key Qi
gpg (GnuPG) 2.2.27; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa3072/4C54AEBAFD434AEC
     created: 2023-08-30  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa3072/023037097B4203A4
     created: 2023-08-30  expires: never       usage: E
[ultimate] (1). Qi Liu

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 8

Possible actions for a RSA key: Sign Encrypt Authenticate
Current allowed actions: Sign Encrypt

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? a

Possible actions for a RSA key: Sign Encrypt Authenticate
Current allowed actions: Sign Encrypt Authenticate

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for a RSA key: Sign Encrypt Authenticate
Current allowed actions: Encrypt Authenticate

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? e

Possible actions for a RSA key: Sign Encrypt Authenticate
Current allowed actions: Authenticate

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Fri Aug 30 08:59:26 2024 CST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  rsa3072/4C54AEBAFD434AEC
     created: 2023-08-30  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa3072/023037097B4203A4
     created: 2023-08-30  expires: never       usage: E
ssb  rsa4096/5F38442EB66E083F
     created: 2023-08-31  expires: 2024-08-30  usage: A
[ultimate] (1). Qi Liu

gpg> save
```
看一下是否添加成功，并查看对应的keygrip
```
$ gpg --list-keys --with-keygrip
/home/liuqi/.gnupg/pubring.kbx
------------------------------
pub   rsa3072 2023-08-30 [SC]
      BC9313A0F060B81897FA8FEE4C54AEBAFD434AEC
      Keygrip = 2706DBDBA8EA19A52A45A2DBED0423B5D409FFB0
uid           [ultimate] Qi Liu
sub   rsa3072 2023-08-30 [E]
      Keygrip = 493DAF49F0A6D36E91DE0F1171EE4979CD7BBD66
sub   rsa4096 2023-08-31 [A] [expires: 2024-08-30]
      Keygrip = D8482CC684C888C6E115F0DBCA91F1A2166BE8BC
```
查看ssh公钥.(需要添加到服务器对应用户的ssh目录配置中，如root用户则应将该公钥附加在`/root/.ssh/authorized_keys`文件的最后)
```
gpg --export-ssh-key qi
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDCKdER9FxFpROG7o/mgrwZ+ilTGz1tvpS1v3BJP1rSgSuWGCZ0bxHEfQmOcUun0qsMXMGgcTMVXlqEuikX9HyG9uwCeKupt/DXlg3WvqLVrQaMpXdi9a502Ob0w7mzsT8ru9hlJRfcnzc6DbKR9XUSFod6IR9iaSayP5E2cSpKlq/jkm7j2pAzkWOE/UgdwCOBOYOvAPUQMa3Vy/nfOwFyBmtwKiPSsrCcOZqmGkYL1dEy8LoVW+9uMTcQOw7ZGEf7si2U41W6al+Cg3St9PYfT0r2DRrkcjEYGwDHuUy5EBx0nwXMrCKoQ4t5BsXuP0muWXaCoj7SIUrWhxIZ7TJQkFPPHaEQ0m6MFmq3Ws+lBKmPpN7zmqoWGz1hm2k1duwNDekQgcniUJ1kN1y8jOL+x61GXgkmEPdPhU9Y4kkImSnZScHjBHWyfuQI5ijAKXk+/848XfcOgA7W769AmDC2HMllYtleJ2+ZqHX8gSBfI2ZYjxVq/K3RIOzvaU0bYW2pJgIJzcjnBHqZZEKU01HgtF59tyCnyq8+3vjRyu7Gk6BPPiC73/ry0HAaCn6071XvVbMOTHOQK/nM8HyZ0gaumJgDbfqfupMYgI69B7en4V/26qNtr2H3VjjKksCbSiHypleNCJBIzZxaSD7UIbAc/xjddL4Qj0vC3kGHSUk3Bw== openpgp:0xB66E083F
```

将上面查到的用于ssh登录的证书对应的keygrip添加到sshcontrol文件中，以让ssh知道证书该用哪个
```
echo D8482CC684C888C6E115F0DBCA91F1A2166BE8BC >> .gnupg/sshcontrol
```

启用gpg的ssh代理(由于改变了配置，可能需要`systemctl --user reload gpg-agent`命令来生效)
```
echo enable-ssh-support >> ~/.gnupg/gpg-agent.conf
```

查看ssh密钥是否在ssh代理中可用
```
$ ssh-add -L
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDCKdER9FxFpROG7o/mgrwZ+ilTGz1tvpS1v3BJP1rSgSuWGCZ0bxHEfQmOcUun0qsMXMGgcTMVXlqEuikX9HyG9uwCeKupt/DXlg3WvqLVrQaMpXdi9a502Ob0w7mzsT8ru9hlJRfcnzc6DbKR9XUSFod6IR9iaSayP5E2cSpKlq/jkm7j2pAzkWOE/UgdwCOBOYOvAPUQMa3Vy/nfOwFyBmtwKiPSsrCcOZqmGkYL1dEy8LoVW+9uMTcQOw7ZGEf7si2U41W6al+Cg3St9PYfT0r2DRrkcjEYGwDHuUy5EBx0nwXMrCKoQ4t5BsXuP0muWXaCoj7SIUrWhxIZ7TJQkFPPHaEQ0m6MFmq3Ws+lBKmPpN7zmqoWGz1hm2k1duwNDekQgcniUJ1kN1y8jOL+x61GXgkmEPdPhU9Y4kkImSnZScHjBHWyfuQI5ijAKXk+/848XfcOgA7W769AmDC2HMllYtleJ2+ZqHX8gSBfI2ZYjxVq/K3RIOzvaU0bYW2pJgIJzcjnBHqZZEKU01HgtF59tyCnyq8+3vjRyu7Gk6BPPiC73/ry0HAaCn6071XvVbMOTHOQK/nM8HyZ0gaumJgDbfqfupMYgI69B7en4V/26qNtr2H3VjjKksCbSiHypleNCJBIzZxaSD7UIbAc/xjddL4Qj0vC3kGHSUk3Bw== (none)
```

一切完成，登录试试看
```
$ ssh root@server_ip
Last login: Thu Aug 31 01:43:54 2023 from 0.0.0.0
...
```


