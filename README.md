知识图谱+网络安全
====
# Knowledge-Graph-Analyze
## 尝试1：
Bro和Snort的初步结果存入知识图谱，如网络包、网络底层事件等。知识图谱在这些数据的基础上进行分析。  
问题：  
具体是做什么样的分析呢，分析出什么结果？  
老师提及的“多步攻击”？  
知识图谱中的数据的存储格式是不是要作出改变，以适应“分析”的要求？  
关于网络底层事件：  
网络基本事件，Bro会生成很多日志文件，其中大多数是以协议的名称命名的（其内容基本是与该协议相关的流量内容）。但是也有比较特殊的日志文件，比如notice.log，我们可以定制该文件的内容（通过添加notice类型的方式），姑且认为notice.log文件中记录的内容就是所谓的网络基本事件。  
conn.log中存放网络中连接的日志，其实连接建立也是一种事件，是不是被Bro整理为日志输出的内容，都属于事件的范畴？  
关于网络包：  
网络包应该是网络流量最原始的状态，没有经过上层分析。Snort在Packet Logger模式下，记录的就是网络数据包。  
关于知识图谱的分析、推理功能：  
参考《网络空间安全防御与态势感知》的第8章，要对网络中的事件坐初步的分析、推理需要一个”本体模型“，这里提及了OWL模型。所以，我们的数据是不是也需要经过一番处理，转换成OWL模型的数据，方便分析、推理呢？  
关于知识图谱的存储：  
我们目前将知识存储在MYSQL数据库中，这种传统的关系型数据的存储与知识图谱所需的语义存储相去甚远。考虑使用D2RQ将关系型数据转换为RDF表示的数据。  

## 数据集选取
考虑DARPA的[LLS_DDOS](https://archive.ll.mit.edu/ideval/data/2000/LLS_DDOS_1.0.html)，这是一个DDOS攻击的数据集，它将攻击分为五个阶段[1]:  
(1) 预探测网络（IPSweep）;  
IPsweep of the AFB from a remote site  
The adversary performs a scripted IPsweep of multiple class C subnets on the Air Force Base. The following networks are swept from address 1 to 254: 172.16.115.0/24, 172.16.114.0/24, 172.16.113.0/24, 172.16.112.0/24. The attacker sends ICMP echo-requests in this sweep and listens for ICMP echo-replies to determine which hosts are "up".  
(2) 端口扫描，确定主机的脆弱信息（PortScan）;  
Probe of live IP's to look for the sadmind daemon running on Solaris hosts  
The hosts discovered in the previous phase are probed to determine which hosts are running the "sadmind" remote administration tool. This tells the attacker which hosts might be vulnerable to the exploit that he/she has. Each host is probed, by the script, using the "ping" option of the sadmind exploit program, as provided on the Internet by "Cheez Whiz". The ping option makes a rpc request to the host in question, asks what TCP port number to connect to for the sadmind service, and then connects to the port number supplied to test to see if the daemon is listening.  
(3) 获得管理员权限（FTPBufOverflow）;  
Breakins via the sadmind vulnerability, both successful and unsuccessful on those hosts  
The attacker then tries to break into the hosts found to be running the sadmind service in the previous phase. The attack script attempts the sadmind Remote-to-Root exploit several times against each host, each time with different parameters. Since this is a remote buffer-overflow attack, the exploit code cannot easily determine the appropriate stack pointer value as in a local buffer-overflow. Thus the adversary must try several different stack pointer values, each of which he/she has validated to work on some test machines. There are three stack pointer values attempted on each potential victim. With each attempt, the exploit tries to execute one command, as root, on the remote system. The attacker needs to execute two commands however, one to "cat" an entry onto the victim's /etc/passwd file and one to "cat" an entry onto the victim's /etc/shadow file. The new root user's name is 'hacker2' and hacker2's home directory is set to be /tmp. Thus, there are 6 exploit attempts on each potential victim host. To test weather or not a break-in was successful, the attack script attempts a login, via telnet, as hacker2, after each set of two breakin attempts. When successful the attackers script moves on to the next potential victim.  
(4) 安装特洛伊Mstream DDOS木马软件（UploadSoftware）;  
Installation of the trojan mstream DDoS software on three hosts at the AFB  
(5) 借助被控制的主机对远程服务器发动DDOS攻击（DDOSAttack）;  
Launching the DDoS  

[1] 胡倩.基于多步攻击场景的攻击预测方法[J].计算机科学,2019,46(S1):365-369.  
 