# day41 dig
dig，DNS lookup utility 的简称，用以解析某个域名的 IP 地址。  
www.baidu.com CNAME 到了 www.a.shifen.com  
www.baidu.com有一个别名，就是www.a.shifen.com  
但是无法直接访问www.a.shifen.com  
www.a.shifen.com 做了负载均衡

```bash
yinzhuo@yinzhuodeMacBook-Pro.local:/Users/yinzhuo $ dig www.baidu.com

; <<>> DiG 9.10.6 <<>> www.baidu.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12382
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.baidu.com.			IN	A

;; ANSWER SECTION:
www.baidu.com.		905	IN	CNAME	www.a.shifen.com.
www.a.shifen.com.	71	IN	A	220.181.38.150
www.a.shifen.com.	71	IN	A	220.181.38.149

;; Query time: 24 msec
;; SERVER: 114.114.114.114#53(114.114.114.114)
;; WHEN: Fri Aug 19 09:46:17 CST 2022
;; MSG SIZE  rcvd: 101
```

nslookup 好像看得更加清晰  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660874315600-4a90b3eb-dbc6-4f4e-9b43-55dee441f9ba.png#clientId=u2b08a46e-05b2-4&from=paste&height=157&id=u90147936&name=image.png&originHeight=157&originWidth=617&originalType=binary&ratio=1&rotation=0&showTitle=false&size=27659&status=done&style=none&taskId=u7325b138-f358-454c-9ca6-f9d0d455fe3&title=&width=617)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/xd09i5