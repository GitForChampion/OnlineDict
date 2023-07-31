# OnlineDict
基于TCP/IP协议Linux在线词典

TCP编程中我们需要用到socket、bind、listen、accept、connect等函数

### socket()函数
```c
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>
int socket(int domain, int type, int protocol);
```
参数：
domain：
| Name | Purpose | Man page |
| --- | --- | --- |
| AF_INET | IPv4 Internet protocols | ip(7) |
| AF_INET6 | IPv6 Internet protocols | ipv6(7) |
| AF_UNIX | Local communication | unix(7) |
| AF_NETLINK | Kernel user interface device | netlink(7) |
| AF_PACKET | Low-level packet interface | packet(7) |

type:
SOCK_STREAM：流式套接字，唯一对应于TCP
SOCK_DGRAM：数据报套接字，唯一对应于UDP
SOCK_RAW：原始套接字

protocol：
一般填0，原始套接字编程时需填充
返回值：成功时返回文件描述符，出错返回-1


### bind()函数

```c
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>
int bind(int sockfd, const struct sockaddr *addr,socklen_t addrlen);
```

返回值：成功0，出错-1
参数：
sockfd：通过socket()函数拿到的fd
addr：struct sockaddr的结构体变量的地址
addrlen：地址长度
struct sockaddr {
		sa_family_t sa_family;
		char        sa_data[14];
}
```c
//基于Internet通信的结构体
struct sockaddr_in {
    sa_family_t    sin_family; /* address family: AF_INET */
    in_port_t      sin_port;   /* port in network byte order */
    struct in_addr sin_addr;   /* internet address */
};

/* Internet address. */
struct in_addr {
    uint32_t       s_addr;     /* address in network byte order */
};
```
实际填充`sockaddr_in`然后强制转换为`sockaddr`
如果是IPV6的编程，要使用struct sockaddr_in6结构体（消息参考man 7 ipv6），通常更通用的方法可以通过struct sockaddr_storage来编程


### listen()函数
```c
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>
int listen(int sockfd, int backlog);
```
返回值：成功0，出错-1
参数：
sockfd：通过socket（）函数拿到的fd
backlog：同时允许几路客户端和服务器进行正在连接的过程（正在三次握手），一般填5，测试的值，ARM最大位8

内核中服务器的套接字fd会维护两个链表：
- 正在三次握手的客户端链表（数量=2*backlog+1）
- 已经建立好连接的客户端链表（已经完成3次握手分配好了newfd）


### accept()函数
阻塞等待客户端连接请求
```c
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```
返回值：成功返回已经建立好连接的新的newfd，出错-1
参数：
sockfd：经过前面socket()创建并通过bind()，listen()设置过的fd



### connect()函数
```c
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>
int connect(int sockfd, const struct sockaddr *addr,socklen_t addrlen);
```
connect()函数和服务器bind()函数写法类似
返回值：成功0，出错-1

参数：
sockfd：通过socket()函数拿到的fd
addr：struct sockaddr的结构体变量的地址
addrlen：地址长度

