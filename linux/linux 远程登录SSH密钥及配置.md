@[toc](linux 远程登录SSH密钥及配置)   

# 密钥备注管理
**SSH密钥自动生成(尽可能添加密钥注释.注释可以是编号,方便后期维护管理)**
> 公钥pub内最后的注释部分生成后也是可以直接修改的不影响密钥使用
> 
**公钥如图(生成的时候加-C 可以自动生成,后期要可以修改):**  
![img1]


# 密钥生成
````shell
#-P为指定密码
ssh-keygen -t rsa -P "" -b 2048 -C "20181205-110001" -f id_rsa_2048_20181205-110001
````
````shell
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130001" -f id_rsa_2048_p_20181205-130001
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130002" -f id_rsa_2048_p_20181205-130002
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130003" -f id_rsa_2048_p_20181205-130003
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130004" -f id_rsa_2048_p_20181205-130004
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130005" -f id_rsa_2048_p_20181205-130005
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130006" -f id_rsa_2048_p_20181205-130006
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130007" -f id_rsa_2048_p_20181205-130007
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130008" -f id_rsa_2048_p_20181205-130008
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130009" -f id_rsa_2048_p_20181205-130009
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130010" -f id_rsa_2048_p_20181205-130010
ssh-keygen -t rsa -P "keypasswd" -b 2048 -C "20181205-130011" -f id_rsa_2048_p_20181205-130011
````

# 用ssh-copy-id将公钥复制到远程机器中 ssh-copy-id 将key写到远程机器的
> ~/.ssh/authorized_key.文件中
> 执行成功会创建空authorized_keys文件，授予600权限（注意：此处权限必须是600）
````shell
ssh-copy-id -i .ssh/id_rsa.pub  <user>@<ip>  #不指定user默认为root
````
````shell
#指定本地用户
ssh-copy-id -u <user> -i /home/<user>/.ssh/id_rsa.pub <user>@<ip>
````

# SSH服务的配置
1. 配置SSH服务的运行参数，是通过修改配置文件/etc/ssh/sshd_config实现的。
2. 因为SSH服务使用默认的配置已经能够很好的工作，如果仅仅提供SSH服务不需要修改。这里只介绍一些常用的选项。
````
Port 22
#定义了SSH服务监听的断口号，SSH服务默认使用的端口号是22
Proctocol 2,1
#定义了SSH服务器使用SSH协议的顺序。默认识先使用SSH2协议，如果不成功则使用SSH1协议，为了安全起见，可以设置只使用SSH2协议。
ListenAddress 0.0.0.0
#定义SSH服务器帮定的IP地址,默认绑定服务器所有可用的IP地址.
PermitRootLogin yes
#定义是否允许管理员登陆
PermitEmptyPasswords no
#定义是否允许空密码登陆.
PasswordAuthentication no
#定义是否使用口令认证方式,如果准备使用公钥认证可以设置为no
````

# 修改私钥密码
````shell
ssh-keygen -f id_rsa -p
````

# 删除known_hosts中这个主机的相关信息
````shell
ssh-keygen -f "~/.ssh/known_hosts" -R <ip>
````

# 连接远程主机
````shell
ssh <user@hostname>   
ssh <hostname> #默认为root登录
#或者
ssh <IP> -l <user>
````

# 指定密钥名称和端口
````shell
ssh -i ~/.ssh/<mykey> <user>@<ip> -p <port>
````

# UseDNS (加速ssh登录速度)
````UseDNS no````

> UseDNS 选项打开状态下，当客户端试图登录SSH服务器时，服务器端先根据客户端的IP地址进行DNS
> PTR反向查询出客户端的主机名，然后根据查询出的客户端主机名进行DNS正向A记录查询，验证与其原始IP地址是否一致，这是防止客户端欺骗的一种措施，但一般我们的是动态IP不会有PTR记录，打开这个选项不过是在白白浪费时间而已，不如将其关闭。

# 指定默认密钥(方便使用可以不用再用-i参数)
````vim /etc/ssh/ssh_config````


**如图修改(不指定全部为默认,特殊情况才需要指定):**
![img2]

# 通过SSH挂载目录/文件系统

````shell
sshfs name@server:/path/to/folder /path/to/mount/point
````


# ssh-add命令
ssh-add命令是把专用密钥添加到ssh-agent的高速缓存中。该命令位置在/usr/bin/ssh-add
````shell
#把专有密钥添加到 ssh-agent 中
ssh-add ~./ssh/id_rsa
#从 ssh-agent 中删除密钥
ssh-add -d ./ssh/id_rsa.pub
#查看 ssh-agent 中的密钥
ssh-add -l

ssh -A <ip>
````

> 注意 ssh 到第一台server的时候，使用 -A 选项 ssh -A 开启认证代理连接转发功能 之后登陆其他 server
> 的时候，只支持 ip 地址访问，除非在 server 的 /etc/hosts 里面添加配置文件. 有没有 -A 选项均可登陆。
> 
````shell
ssh-agent 
SSH_AUTH_SOCK=/tmp/ssh-QeRbs44L8PQN/agent.22875; export SSH_AUTH_SOCK;
SSH_AGENT_PID=22876; export SSH_AGENT_PID;
echo Agent pid 22876;

ssh-add
Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)

ssh -A x163
Last login: Fri Aug 12 05:19:44 2018 from x1

ssh -A <ip>
Last login: Fri Aug 12 05:39:44 2018 from <ip>
````


# ssh-keygen可用的参数选项有：
````
     -a trials
             在使用 -T 对 DH-GEX 候选素数进行安全筛选时需要执行的基本测试数量。

     -B      显示指定的公钥/私钥文件的 bubblebabble 摘要。

     -b bits
             指定密钥长度。对于RSA密钥，最小要求768位，默认是2048位。DSA密钥必须恰好是1024位(FIPS 186-2 标准的要求)。

     -C comment
             提供一个新注释

     -c      要求修改私钥和公钥文件中的注释。本选项只支持 RSA1 密钥。
             程序将提示输入私钥文件名、密语(如果存在)、新注释。

     -D reader
             下载存储在智能卡 reader 里的 RSA 公钥。

     -e      读取OpenSSH的私钥或公钥文件，并以 RFC 4716 SSH 公钥文件格式在 stdout 上显示出来。
             该选项能够为多种商业版本的 SSH 输出密钥。

     -F hostname
             在 known_hosts 文件中搜索指定的 hostname ，并列出所有的匹配项。
             这个选项主要用于查找散列过的主机名/ip地址，还可以和 -H 选项联用打印找到的公钥的散列值。

     -f filename
             指定密钥文件名。

     -G output_file
             为 DH-GEX 产生候选素数。这些素数必须在使用之前使用 -T 选项进行安全筛选。

     -g      在使用 -r 打印指纹资源记录的时候使用通用的 DNS 格式。

     -H      对 known_hosts 文件进行散列计算。这将把文件中的所有主机名/ip地址替换为相应的散列值。
             原来文件的内容将会添加一个".old"后缀后保存。这些散列值只能被 ssh 和 sshd 使用。
             这个选项不会修改已经经过散列的主机名/ip地址，因此可以在部分公钥已经散列过的文件上安全使用。

     -i      读取未加密的SSH-2兼容的私钥/公钥文件，然后在 stdout 显示OpenSSH兼容的私钥/公钥。
             该选项主要用于从多种商业版本的SSH中导入密钥。

     -l      显示公钥文件的指纹数据。它也支持 RSA1 的私钥。
             对于RSA和DSA密钥，将会寻找对应的公钥文件，然后显示其指纹数据。

     -M memory
             指定在生成 DH-GEXS 候选素数的时候最大内存用量(MB)。

     -N new_passphrase
             提供一个新的密语。

     -P passphrase
             提供(旧)密语。

     -p      要求改变某私钥文件的密语而不重建私钥。程序将提示输入私钥文件名、原来的密语、以及两次输入新密语。

     -q      安静模式。用于在 /etc/rc 中创建新密钥的时候。

     -R hostname
             从 known_hosts 文件中删除所有属于 hostname 的密钥。
             这个选项主要用于删除经过散列的主机(参见 -H 选项)的密钥。

     -r hostname
             打印名为 hostname 的公钥文件的 SSHFP 指纹资源记录。

     -S start
             指定在生成 DH-GEX 候选模数时的起始点(16进制)。

     -T output_file
             测试 Diffie-Hellman group exchange 候选素数(由 -G 选项生成)的安全性。

     -t type
             指定要创建的密钥类型。可以使用："rsa1"(SSH-1) "rsa"(SSH-2) "dsa"(SSH-2)

     -U reader
             把现存的RSA私钥上传到智能卡 reader

     -v      详细模式。ssh-keygen 将会输出处理过程的详细调试信息。常用于调试模数的产生过程。
             重复使用多个 -v 选项将会增加信息的详细程度(最大3次)。

     -W generator
             指定在为 DH-GEX 测试候选模数时想要使用的 generator

     -y      读取OpenSSH专有格式的公钥文件，并将OpenSSH公钥显示在 stdout 上。
````

# sshd_conf逐行说明上面的选项设置：
````
Port 22
"Port"设置sshd监听的端口号。
ListenAddress 192.168.1.1
"ListenAddress”设置sshd服务器绑定的IP地址。
HostKey /etc/ssh/ssh_host_key
"HostKey”设置包含计算机私人密匙的文件。
ServerKeyBits 1024
"ServerKeyBits”定义服务器密匙的位数。
LoginGraceTime 600
"LoginGraceTime”设置如果用户不能成功登录，在切断连接之前服务器需要等待的时间（以秒为单位）。
KeyRegenerationInterval 3600
"KeyRegenerationInterval”设置在多少秒之后自动重新生成服务器的密匙（如果使用密匙）。重新生成密匙是为了防止用盗用的密匙解密被截获的信息。
PermitRootLogin no
"PermitRootLogin”设置是否允许root通过ssh登录。这个选项从安全角度来讲应设成"no"。
IgnoreRhosts yes
"IgnoreRhosts”设置验证的时候是否使用“rhosts”和“shosts”文件。
IgnoreUserKnownHosts yes
"IgnoreUserKnownHosts”设置ssh daemon是否在进行RhostsRSAAuthentication安全验证的时候忽略用户的"$HOME/.ssh/known_hosts”
StrictModes yes
"StrictModes”设置ssh在接收登录请求之前是否检查用户家目录和rhosts文件的权限和所有权。这通常是必要的，因为新手经常会把自己的目录和文件设成任何人都有写权限。
X11Forwarding no
"X11Forwarding”设置是否允许X11转发。
PrintMotd yes
"PrintMotd”设置sshd是否在用户登录的时候显示“/etc/motd”中的信息。
SyslogFacility AUTH
"SyslogFacility”设置在记录来自sshd的消息的时候，是否给出“facility code”。
LogLevel INFO
"LogLevel”设置记录sshd日志消息的层次。INFO是一个好的选择。查看sshd的man帮助页，已获取更多的信息。
RhostsAuthentication no
"RhostsAuthentication”设置只用rhosts或“/etc/hosts.equiv”进行安全验证是否已经足够了。
RhostsRSAAuthentication no
"RhostsRSA”设置是否允许用rhosts或“/etc/hosts.equiv”加上RSA进行安全验证。
RSAAuthentication yes
"RSAAuthentication”设置是否允许只有RSA安全验证。
PasswordAuthentication yes
"PasswordAuthentication”设置是否允许口令验证。
PermitEmptyPasswords no
"PermitEmptyPasswords”设置是否允许用口令为空的帐号登录。
AllowUsers admin
"AllowUsers”的后面可以跟任意的数量的用户名的匹配串，这些字符串用空格隔开。主机名可以是域名或IP地址。
````






<!-- ---------------------- -->
[img1]:./images/1.png
[img1]:./images/2.png