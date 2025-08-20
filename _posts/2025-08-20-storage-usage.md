---
layout: post
title: "리눅스 스토리지 사용량 많은 파일 경로 조회"
date: 2025-08-20 13:32:00 +0900
categories: Linux
---

## 리눅스 스토리지 사용량 많은 파일 경로 조회

```bash
sudo find /APP -type f -exec du -h {} + | sort -rh | head -n 20

du -h | sort -rh | head -n 100 -> 이게 좀 더 나은듯
```
