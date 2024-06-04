# Configurações Caliandra

## 1. Limpar todas as regras existentes

```
iptables -F
iptables -t nat -F
iptables -t mangle -F
iptables -X
```
- Remove todas as regras e cadeias definidas no iptables.
- Garante a inicialização com uma configuração limpa.

<br></br>

## 2. Definir política padrão para INPUT, FORWARD e OUTPUT

```
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT
```
