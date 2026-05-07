# Vault

Устанавливает и настраивает HashiCorp Vault с бэкапом снапшотов в Cloudflare R2.

## Порядок действий:

### 1. Прогнать роль

### 2. Инициализировать Vault

```bash
VAULT_ADDR=http://127.0.0.1:8200 vault operator init
```

### 3. Распечатать Vault

```bash
VAULT_ADDR=http://127.0.0.1:8200 vault operator unseal
```

### 4. Создать токен для бэкапа

```bash
VAULT_ADDR=http://127.0.0.1:8200 vault login

vault policy write backup - <<EOF
path "sys/storage/raft/snapshot" {
  capabilities = ["read"]
}
EOF

vault token create -policy=backup -period=0 -no-default-policy
```

### 5. Положить креды в vault.yml и перепрогнать роль

Бэкап будет запускаться ежедневно в 04:00

## Восстановить из снапшота

```bash
rclone copy r2:bootstrap-backups/vault/vault-snapshot-YYYYMMDDHHMMSS.snap /tmp/

VAULT_ADDR=http://127.0.0.1:8200 vault operator raft snapshot restore /tmp/vault-snapshot-YYYYMMDDHHMMSS.snap
```
