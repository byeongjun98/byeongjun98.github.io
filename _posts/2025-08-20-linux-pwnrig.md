---
layout: post
title: "리눅스 pwnrig 공격 대처"
date: 2025-08-20 13:32:00 +0900
categories: Linux
---

### pwnrig란

- 가상화폐 채굴 프로그램으로 멜웨어 중 하나라고 보면 될 것 같다

### 대처방법

```bash
# 팀원분이 찾아주신 방법
sudo systemctl list-units --type=service | grep pwnrig
sudo systemctl stop [service이름]
sudo systemctl  disable [service이름]
sudo chattr -ia /etc/systemd/system/[service이름] 
sudo rm -rf /etc/systemd/system/[service이름]

sudo systemctl daemon-reload

find / -type f -name pwnrig | xargs chattr -ia && find ./etc -type f -name pwnrig | xargs rm -rf

# 다 수행 후 ssh 포트 변경 
```

```bash
# chat-gpt
ps aux | grep pwnrig # pid 확인 후 kill -9

crontab -l # 크론탭 조회
crontab -r # 크론탭 제거

# 조회 해보고 관련된 파일 있으면 제거
# Ex) sudo rm -rf /etc/cron.d/pwnrig

ls -lah /etc/cron.d/
ls -lah /etc/cron.daily/
ls -lah /etc/cron.hourly/
ls -lah /etc/cron.monthly/
ls -lah /etc/cron.weekly/

# 조회해보고 관련된 파일 있으면 제거
# Ex) sudo rm -rf /tmp/pwnrig*
#     sudo rm -rf /var/tmp/pwnrig*
ls -lah /tmp/
ls -lah /var/tmp/

# 서비스 목록 조회
# 관련된 내용 있다면 제거
# Ex) sudo systemctl stop [서비스이름]
#     sudo systemctl disable [서비스이름]
#     sudo rm -rf /etc/systemd/system/[서비스이름].service
#     sudo systemctl daemon-reload
systemctl list-units --type=service | grep -i pwnrig
```
