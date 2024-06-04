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
