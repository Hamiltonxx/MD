# trojan

airust.cirray.cn 74.48.60.101 root poiu0987 nTRcd13B61 Mhw238nEdT l72cMXASn5

cal.cirray.cn 66.112.213.38 hamilton 09270709

# tencent

sh.cirray.cn py.cirray.cn 43.142.157.226 ubuntu WwXx0602

# ali

cms.cirray.cn 106.14.244.205 ubuntu 09270709

# acme

```
export Ali_Key=""
export Ali_Secret=""

acme.sh --issue --dns dns_ali -d aigc.cirray.cn
```

# 跳板机

```
ssh -J hamilton@cal.cirray.cn ubuntu@107.23.14.119

scp -o ProxyJump=hamilton@cal.cirray.cn ubuntu@107.23.14.119:~/projects/the9cms.zip .
```

# X99

```
ssh keke@sh.cirray.cn -p 7022

# 运行sh3
cd /mnt/mydisk/photos
python simpleserver2.py
# https://sh3.cirray.cn:9000
# 运行forgejo
cd /mnt/mydisk/workbench
./forgejo-8.0.2-linux-amd64
# https://git.cirray.cn
# cirray   09270709
```

# rsync

```
rsync -avz --partial --progress -e ssh ~/projects/the9-official-web/dist/ ubuntu@107.23.14.119:/data/web/the9/

rsync -avz --partial --progress -e ssh ~/projects/the9-official-web/dist/ ubuntu@107.23.14.119:~/temp/dist/
```

# sshfs

```
brew install sshfs
mkdir ~/remote-server
sshfs keke@sh.cirray.cn:/mnt/mydisk ~/remote-server
```

```
umount ~/remote-server
diskutil unmount force ~/remote_folder
```
