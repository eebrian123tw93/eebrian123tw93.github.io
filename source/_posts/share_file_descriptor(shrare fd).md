---
title: share_file_descriptor(shrare fd)
tags:
  - IPC
  - fd
  - linux
categories:
  - IPC
date: '2024-03-19 15:10:00'
updated: '2024-03-19 07:05:34'
description: ''
---
# 前言
最近在處理影像buffer的時，範例在建立buffer記憶體是用其他 library去生成，影像 buffer 是通過mmap 去取的，但是我需要其他進程也可以拿到這個 buffer ，

有種做法是完整複製buffer，然後開一塊share memory處理，但是這樣會造成cpu一直在copy
還有種作法是找到 file descriptor 參照哪個檔案，但查詢了許久，我無法得知 file descriptor 是參照哪個檔案，
 <!-- more -->
 所以目前的做法是將 mmap 參照的 file descriptor 共享給其他進程使用。
## 解決思路
網路上有找到兩種可行辦法，一個是用syscall ，一個是透過 domain socket
用syscall的辦法很簡單，代碼也很少，但是要linux kernel比較高的版本
所以目前先嘗試使用 domain socket

## server 端 (share_fd_set.c)
``` c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <errno.h> 
#include <sys/un.h>
#include <sys/socket.h>


ssize_t write_fd(int fd, void *ptr, size_t nbytes, int sendfd);

int main() {


    int fd = open("file.txt", O_RDWR | O_CREAT, 0644);
    if (fd < 0) {
        printf("open <%s> failed: %s\n", "file.txt", strerror(errno));
        return -1;
    }

    printf("fd: %d\n", fd); 
  

    int sockfd;
    struct sockaddr_un server_addr, client_addr;
    struct iovec iov[1];
    struct msghdr msg;
    char buffer[100];

    sockfd = socket(AF_UNIX, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("socket error");
        return 1;
    }

    memset(&server_addr, 0, sizeof(server_addr));
    server_addr.sun_family = AF_UNIX;
    unlink("/tmp/socket");
    strcpy(server_addr.sun_path, "/tmp/socket");
    if (bind(sockfd, (struct sockaddr *)&server_addr, sizeof(server_addr)) < 0) {
        perror("bind");
        return 1;
    }

    if (listen(sockfd, 5) == -1) {
        perror("bind");
        return 1;
    }

    printf("等待連接\n");
    socklen_t client_len =  sizeof(client_addr);
    int clientfd = accept(sockfd, (struct sockaddr *)&client_addr, &client_len);
    if (clientfd == -1) {
        perror("accept");
        return 1;
    }

   if (write_fd(clientfd, buffer, 100, fd) < 0) {
        perror("sendmsg error");
        return 1;
    }

    printf("傳輸完畢\n");

    close(sockfd);
    close(clientfd);
    close(fd);

    sleep(10);

    return 0;
}

ssize_t write_fd(int fd, void *ptr, size_t nbytes, int sendfd) {
    struct msghdr   msg;
    struct iovec    iov[1];

    union {
      struct cmsghdr    cm;
      char              control[CMSG_SPACE(sizeof(int))];
    } control_un;
    struct cmsghdr  *cmptr;

    msg.msg_control = control_un.control;
    msg.msg_controllen = sizeof(control_un.control);

    cmptr = CMSG_FIRSTHDR(&msg);
    cmptr->cmsg_len = CMSG_LEN(sizeof(int));
    cmptr->cmsg_level = SOL_SOCKET;
    cmptr->cmsg_type = SCM_RIGHTS;
    *((int *) CMSG_DATA(cmptr)) = sendfd;

    msg.msg_name = NULL;
    msg.msg_namelen = 0;

    iov[0].iov_base = ptr;
    iov[0].iov_len = nbytes;
    msg.msg_iov = iov;
    msg.msg_iovlen = 1;

    return(sendmsg(fd, &msg, 0));
}

```
## client 端 (share_fd_get.c)
``` c

#include <sys/mman.h>
#include <unistd.h>
#include <errno.h> 
#include <sys/msg.h>
#include <stdio.h>
#include <sys/socket.h>
#include <sys/un.h>
#include <stdlib.h>

ssize_t read_fd(int fd, void *ptr, size_t nbytes, int *recvfd);


int main() {
    int sockfd;
    struct sockaddr_un server_addr;

    char buffer[100];

    sockfd = socket(AF_UNIX, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("socket error");
        return -1;
    }

    // 设置服务器地址结构体
    memset(&server_addr, 0, sizeof(server_addr));
    server_addr.sun_family = AF_UNIX;
    strcpy(server_addr.sun_path, "/tmp/socket");

    // 绑定套接字
    if (connect(sockfd, (const struct sockaddr *)&server_addr, sizeof(server_addr)) < 0) {
        perror("bind");
        return -1;
    }

    
    int targetfd;
    int ret = read_fd(sockfd, buffer, 100, &targetfd);
    if (ret < 0) {
        perror("read_fd error");
        return -1;
    }
    printf ("receive %d %s\n", 100, buffer);
 
    void *address = mmap(NULL, 100, PROT_READ, MAP_SHARED, targetfd, 0);
    printf ("targetfd %d ,address %p\n", targetfd, address);
    close(targetfd);


    if (sockfd >= 0) {
        close(sockfd);
    }

    return 0;
}


ssize_t read_fd(int fd, void *ptr, size_t nbytes, int *recvfd) {

    struct msghdr   msg;
    struct iovec    iov[1];
    ssize_t         n;
    int             newfd;


    union {
      struct cmsghdr    cm;
      char              control[CMSG_SPACE(sizeof(int))];
    } control_un;
    struct cmsghdr  *cmptr;

    msg.msg_control = control_un.control;
    msg.msg_controllen = sizeof(control_un.control);


    msg.msg_name = NULL;
    msg.msg_namelen = 0;

    iov[0].iov_base = ptr;
    iov[0].iov_len = nbytes;
    msg.msg_iov = iov;
    msg.msg_iovlen = 1;

    printf("recvmsg\n");

    if ( (n = recvmsg(fd, &msg, 0)) <= 0) {
        return(n);
    }
        
    printf("recvmsg n %ld\n", n);
    cmptr = CMSG_FIRSTHDR(&msg);
    printf("cmptr %p\n", cmptr);

    if ( cmptr != NULL &&
        cmptr->cmsg_len == CMSG_LEN(sizeof(int))) {
        if (cmptr->cmsg_level != SOL_SOCKET) {
            printf("control level != SOL_SOCKET\n");
            exit(1);
        } 
           
        if (cmptr->cmsg_type != SCM_RIGHTS) {
            printf("control type != SCM_RIGHTS");
            exit(1);
        }
            
        *recvfd = *((int *) CMSG_DATA(cmptr));
    } else
        *recvfd = -1;       /* descriptor was not passed */


    return(n);
}
```
## 編譯
``` bash
gcc  share_fd_set.c -o share_fd_set
gcc  share_fd_get.c -o share_fd_get
chmod +x share_fd_set
chmod +x share_fd_get
```
## 執行
```
./share_fd_set
fd: 3
等待連接
./share_fd_get
recvmsg
recvmsg n 100
cmptr 0x7ffd7979b020
receive 100 
targetfd 4 ,address 0x7ffbceec2000
```
# 參考
- [stackoverflow share fd](https://stackoverflow.com/questions/2358684/can-i-share-a-file-descriptor-to-another-process-on-linux-or-are-they-local-to-t/2358843#2358843)
- [Linux通过sendmsg来在进程中share FD](https://www.cnblogs.com/super119/archive/2012/09/11/2680006.html)