```zsh
ssh -i ~/.ssh/mykey.pem ubuntu@<你的实例公网IP>
```

```zsh
nano ~/.ssh/config

Host aws-ec2
    HostName 52.9.000.00
    User alanwang
    IdentityFile ~/.ssh/mykey.pem
    IdentitiesOnly yes
    ServerAliveInterval 10
    ServerAliveCountMax 3
    TCPKeepAlive yes
    ConnectTimeout 15
    ControlMaster auto
    ControlPath ~/.ssh/cm-%r@%h:%p
    ControlPersist 10m
```

```zsh
ssh aws-ec2
```


 **去掉 macOS 下载标签**（常见坑 ⚠️）：
```zsh
xattr -c ~/.ssh/mykey.pem
```
`com.apple.quarantine` 是 macOS 为了安全自动加的“下载来源标签”。对一般文件没影响，但对 SSH 私钥来说会导致拒绝使用，所以我们需要手动清掉。


# Problem
- **EC2 安全组**没放行 22 端口，或者只对旧 IP 开放。
- **sshd 配置**卡在 DNS/GSSAPI → 建议加 `UseDNS no`、`GSSAPIAuthentication no`。
- **sshd 服务挂了** → 可以用 EC2 Instance Connect（网页终端）进去重启：

## SWAP
```zsh
# 1. Create a 2GB swap file
sudo fallocate -l 2G /swapfile

# 2. Set permissions (must be 600, otherwise the system will reject it)
sudo chmod 600 /swapfile

# 3. Format as swap
sudo mkswap /swapfile

# 4. Enable swap
sudo swapon /swapfile

# 5. Check if it worked
free -m
```