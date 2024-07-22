### docker 容器启动,检测到登录失败3次，封禁IP 3600秒也就是一个小时.

```javascript
cat <<EOF | sudo tee /data/jail.local
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/messages
maxretry = 3
bantime = 3600

#[nginx-http-auth]
#enabled = true
#port = http,https
#filter = nginx-http-auth
#logpath = /var/log/nginx/access.log
#maxretry = 100
#findtime = 60
#bantime = 3600
EOF

docker run -d --name fail2ban \
  -v /var/log:/var/log \
  -v /data/jail.local:/etc/fail2ban/jail.d/jail.local \
  -e TZ=Asia/Shanghai \
  crazymax/fail2ban
```

### 解除IP限制.

```javascript
docker exec -it fail2ban /bin/bash
fail2ban-client reload
fail2ban-client status sshd     #查看封的IP列表
fail2ban-client set sshd unbanip {ip地址}      #解除ssh登录解封一小时限制
```
