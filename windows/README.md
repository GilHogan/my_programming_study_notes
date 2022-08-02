### windows ssh 代理配置
```txt
# 编辑 C:\Users\{userName}\.ssh\config 文件
Host xxx.xxx.xxx.xxx
    ProxyCommand C:\Program Files\Git\mingw64\bin\connect.exe -S 127.0.0.1:10808 %h %p
	IdentityFile "C:\Users\{userName}\.ssh\ssh-key.key"
    ServerAliveInterval 20
	ServerAliveCountMax 3
```