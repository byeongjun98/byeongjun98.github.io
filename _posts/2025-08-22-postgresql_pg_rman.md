---
layout: post
title: "Postgresql 백업 전략"
date: 2025-08-24 17::00 +0900
categories: PostgreSQL
---

PostgreSQL 백업 전략에 대한 정리

## 사용할 tool

- pg_rman

## 백업

#### 백업 경로 준비

```bash
mkdir -p /home/postgres/rman_backup /home/postgres/pg_wal_archive
chown -R postgres:postgres /home/postgres/rman_backup/ /home/postgres/pg_wal_archive
```

#### postgresql.conf 파일 수정

```bash
# 증분 백업을 위한 archive_mode on
222 archive_mode = on               # enables archiving; off, on, or always
223                                 # (change requires restart)
224 archive_command = 'cp %p /home/postgres/pg_wal_archive/%f'              # command to use to archive a logfile segment

# 수정 후 재시작
pg_ctl -D ${PGDATA} -l ${PGSQL}/logs/pgsql.log restart
```

#### pg_rman 카탈로그 초기화

```bash
sudo -u postgres /apps/postgresql/bin/pg_rman init -B /home/postgres/rman_backup -D /apps/postgresql/data
```

#### 풀 백업

```bash
sudo -u postgres /apps/postgresql/bin/pg_rman backup --backup-mode=full -B /home/postgres/rman_backup -D /apps/postgresql/data --compress-data
```

#### 증분 백업

```bash
sudo -u postgres /apps/postgresql/bin/pg_rman backup --backup-mode=incremental -B /home/postgres/rman_backup -D /apps/postgresql/data --compress-data
```

### 크론 등록

- postgres 계정에서 등록

```bash
# 매주 일요일 3시 풀백업
0 3 * * 0 sudo -u postgres /apps/postgresql/bin/pg_rman backup --backup-mode=full -B /home/postgres/rman_backup -D /apps/postgresql/data --compress-data && sudo -u postgres /apps/postgresql/bin/pg_rman validate -B /home/postgres/rman_backup

# 매일 3시 증분백업
0 3 * * 1-6 sudo -u postgres /apps/postgresql/bin/pg_rman backup --backup-mode=incremental -B /home/postgres/rman_backup -D /apps/postgresql/data --compress-data && sudo -u postgres /apps/postgresql/bin/pg_rman validate -B /home/postgres/rman_backup

# 매일 4시, 30일 이전 백업 삭제(로그는 백업경로에 남김)
0 4 * * * sudo -u postgres /apps/postgresql/bin/pg_rman delete --keep-data-generations=30 -B /home/postgres/rman_backup >> /home/postgres/rman_backup/delete.log 2>&1

# 매일 4시 5분, 백업 이력 로그 저장
5 4 * * * sudo -u postgres /apps/postgresql/bin/pg_rman show -B /home/postgres/rman_backup > /home/postgres/rman_backup/backup_history.log
```

### 백업 검증

```bash
sudo -u postgres /apps/postgresql/bin/pg_rman validate -B /home/postgres/rman_backup
```

### 백업 이력 확인

```bash
sudo -u postgres /apps/postgresql/bin/pg_rman show -B /home/postgres/rman_backup
```

## 복구 예시

```bash
sudo systemctl stop postgresql
sudo -u postgres /apps/postgresql/bin/pg_rman restore --recovery-target-time '2024-06-01 03:00:00' -B /home/postgres/rman_backup -D /var/lib/postgresql/15/main
sudo systemctl start postgresql
```

## **pg_rman 설치 방법 (PostgreSQL 15, 우분투 기준)**

### 1. **필수 패키지 설치**

```bash
sudo apt update
sudo apt install -y wget gnupg lsb-release
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /usr/share/keyrings/postgresql.gpg
echo "deb [signed-by=/usr/share/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
sudo apt update
sudo apt install -y git build-essential libpq-dev postgresql-server-dev-15
```

### 2. **소스 다운로드**

```bash
cd /tmp
git clone https://github.com/ossc-db/pg_rman.git
cd pg_rman
```

### 3. **코드 수정 (환경에 맞춰 헤더/인자수 맞추기) // 5번 빌드 및 설치 에서 에러 발생시 수행**

- **backup.c** (890, 2128 line 등)
    
    ```c
    // 기존:
    controlFile = get_controlfile(pgdata, &crc_ok);
    // → 아래처럼 변경:
    controlFile = get_controlfile(pgdata, "pg_rman", &crc_ok);
    ```
    
- **pgut/pgut.c** (898, 903 line 등)
    
    ```c
    // 기존:
    password = simple_prompt("Password: ", false);
    // → 아래처럼 변경:
    char pwbuf[100];
    simple_prompt("Password: ", pwbuf, sizeof(pwbuf), false);
    password = pgut_strdup(pwbuf);
    ```
    

### 4. **(헤더 환경 꼬임 방지) // 5번 빌드 및 설치 에서 에러 발생시 수행**

- **빌드, 설치 모두 동일한 헤더 참조되게 통일**
    - (필요시 심볼릭 링크 또는 -I 옵션 조정)
- **헤더가 꼬이면, 인자수 맞게 소스/헤더 같이 맞춰야 함**

### 5. **빌드 및 설치**

```bash
make clean
make USE_PGXS=1
sudo make install
```

> ※ 설치 위치가 다를 경우,
> 
> 
> `/usr/lib/postgresql/15/bin/pg_rman` 등 실제 설치된 경로에서 원하는 곳(`/apps/postgresql/bin/` 등)으로 직접 복사
> 
> ```bash
> sudo cp /usr/lib/postgresql/15/bin/pg_rman /apps/postgresql/bin/
> sudo chown postgres:postgres /apps/postgresql/bin/pg_rman
> sudo chmod 755 /apps/postgresql/bin/pg_rman
> ```
> 

### 6. **설치 확인**

```bash
/apps/postgresql/bin/pg_rman --version
```

또는

```bash
which pg_rman
pg_rman --version
```