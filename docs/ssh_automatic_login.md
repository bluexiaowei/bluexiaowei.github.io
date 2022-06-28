# ssh 自动登录

## 生成 ssh key
> 如果已经生成密钥自动跳过此步骤

```bash
$ ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa)

Created directory '/home/ubuntu/.ssh'.
Enter passphrase (empty for no passphrase):

Enter same passphrase again:

Your identification has been saved in /home/ubuntu/.ssh/id_rsa.
Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:7LA4zwL2EJDcvPP7VcKrAjQ6uylQJzPBg8kcn0u0do4 xxx
The key's randomart image is:
+---[RSA 3072]----+
|+*+.             |
|=+*oo            |
| . O..           |
|  O=*  ..        |
| .oE+.. So .     |
|.o+. o +  +      |
|..o++ o .o       |
|... o*  o        |
|.o.  .=o         |
+----[SHA256]-----+
```

## 本地公钥

```bash
$ cat ~/.ssh/id_rsa.pub

xxxxx
```

## 配置远程服务器

登录远程服务器 `$ ssh root@xxx.xxx.xxx.xxx`

重复 ## 生成 ssh key

将 ## 本地公钥 复制到远程服务器 `$ vim ~/.ssh/authorized_keys` 文件中。

重新登录远程服务器，验证是否生效