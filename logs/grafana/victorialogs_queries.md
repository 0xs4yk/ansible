# Requêtes VictoriaLogs (LogsQL)

Voici des exemples de requêtes pour exploiter vos logs parsés dans Grafana avec le datasource VictoriaLogs.

## Firewall (UFW)

**Top des IPs bloquées (Source)**
```sql
_stream:{tag="ufw.*"} | stats by (SRC) count() desc
```

**Top des Ports de destination bloqués**
```sql
_stream:{tag="ufw.*"} | stats by (DPT) count() desc
```

**Tentatives de connexion par Protocole**
```sql
_stream:{tag="ufw.*"} | stats by (PROTO) count() desc
```

**Matrice Source -> Destination (Qui attaque quoi)**
```sql
_stream:{tag="ufw.*"} | stats by (SRC, DST) count() desc
```

## Authentification (Auth & Sudo)

**Connexions SSH réussies**
```sql
_stream:{tag="auth.*"} | filter "Accepted" | stats by (user, ip) count() desc
```
*Note : Nécessite un parsing supplémentaire du message SSH ou une regex dans la requête.*

**Utilisation de la commande SUDO**
```sql
_stream:{tag="auth.*"} | filter "sudo" | extract "COMMAND=(?P<cmd>.*)$" | stats by (user, cmd) count() desc
```

## Audit (Auditd)

**Top des appels système (Syscalls)**
```sql
_stream:{tag="audit.*"} | stats by (syscall) count() desc
```

**Fichiers accédés (si path présent)**
```sql
_stream:{tag="audit.*"} | filter path!="" | stats by (path) count() desc
```

## Systemd & Kernel

**Erreurs Systemd par unité**
```sql
_stream:{tag="systemd.*"} | filter priority <= 3 | stats by (SYSTEMD_UNIT) count() desc
```

**Logs Kernel par niveau de priorité**
```sql
_stream:{tag="kernel.*"} | stats by (PRIORITY) count() desc
```
