---
layout: post
title: "xen 서버 vm Grub 복구"
date: 2025-08-22 15:22:00 +0900
categories: Linux
---

xen server (Xcp-ng Center) vm Grub 복구

## 🛠️ GRUB 복구 과정 (Rocky Linux 기준)

### 1. 상황 개요

- VM이 GRUB minimal shell 상태로 부팅됨 (lsblk, fdisk 명령 불가)
- Rocky Linux 8.9 ISO를 활용한 복구 시도

### 2. ISO 마운트 및 부팅

- XCP-ng Center → DVD Drive → ISO_Library → Rocky Linux 8.9 ISO 선택
- VM 부팅 후 “Troubleshooting” → “Rescue a Rocky Linux system” 선택

### 3. Rescue 옵션 선택

- 
    Continue (read-write로 마운트)
- 메시지:
    
    ```
    Your system has been mounted under /mnt/sysroot.
    To chroot: chroot /mnt/sysroot
    ```
    
- 명령어 입력:
    
    ```bash
    chroot /mnt/sysroot
    ```
    

### 4. GRUB 재설치 및 설정 갱신

```bash
grub2-install /dev/xvda
grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 5. 재부팅

- `exit` → `reboot` 로 정상 부팅 확인