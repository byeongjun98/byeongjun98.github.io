---
layout: post
title: "xen 서버 vm 스토리지 증설 및 lvm 확장"
date: 2025-08-22 15:30:00 +0900
categories: Linux
---

xen 서버 vm 스토리지 증설 및 lvm 확장

## 💾 VM 스토리지 용량 증설 및 LVM 확장

### 1. VM 스토리지 확장 (XCP-ng Center)

- VM 종료 후, Properties → Hard Disk 탭에서 디스크 크기 확장

### 2. OS 내부 작업 (LVM 기준)

#### 2-1. 디스크 확인

```bash
lsblk
```

#### 2-2. growpart 설치 및 파티션 확장

```bash
dnf install cloud-utils-growpart -ygrowpart /dev/xvda 2
```

#### 2-3. Physical Volume 확장

```bash
pvresize /dev/xvda2
```

#### 2-4. Logical Volume 및 파일시스템 확장

```bash
lvextend -r -l +100%FREE /dev/rl/root
# 또는lvextend -l +100%FREE /dev/rl/root
xfs_growfs /
```

#### 2-5. 용량 확인

```bash
df -h
```

### ✅ 결과

- 디스크 공간이 LVM과 파일시스템에 반영됨