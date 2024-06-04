# Configurações Caliandra

## 1. Limpar todas as regras existentes

```
iptables -F
iptables -t nat -F
iptables -t mangle -F
iptables -X
```
- Remove todas as regras e cadeias definidas no `iptables`.
- Garante a inicialização com uma configuração limpa.

## 2. Definir política padrão para INPUT, FORWARD e OUTPUT

```
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT
```
- Define políticas padrão para as cadeias de entrada, encaminhamento e saída.
  - INPUT: Bloquear todas as conexões de entrada.
  - FORWARD: Bloquear todas as conexões de encaminhamento.
  - OUTPUT: Permitir todas as conexões de saída.

## 3. Permitir tráfego de loopback

```
iptables -A INPUT -i lo -j ACCEPT
```
- Permite todo o tráfego na interface de loopback (`lo`), que é usada para comunicação interna no sistema.
- Garante que serviços internos no sistema possam se comunicar.

## 4. Permitir tráfego de entrada relacionado e estabelecido

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
- Permite pacotes de entrada que fazem parte de conexões já estabelecidas ou relacionadas.
- Mantém a continuidade de conexões legítimas iniciadas pelo sistema.

## 5. Permitir todo o tráfego de saída para a Internet

```
iptables -A OUTPUT -o eth0 -j ACCEPT
```
- Permite todo o tráfego de saída na interface de rede `eth0`.
- Permite que o sistema envie dados para a Internet.

## 6. Bloquear todo o tráfego de entrada da Internet que não seja uma resposta a solicitações iniciadas internamente

```
iptables -A INPUT -i eth0 -m state --state NEW -j DROP
```
- Bloqueia novos pacotes de entrada na interface `eth0` que não sejam respostas a solicitações iniciadas internamente.
- Previne conexões não solicitadas da Internet.

## 7. Permitir conexões HTTP e HTTPS de entrada da Internet

```
iptables -A INPUT -i eth0 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 443 -j ACCEPT
```
- Permite pacotes TCP de entrada nas portas 80 (HTTP) e 443 (HTTPS).
- Permite que o servidor receba tráfego web.

## 8. Permitir conexões DNS de entrada e saída

```
iptables -A INPUT -i eth0 -p udp --dport 53 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 53 -j ACCEPT
```
- Permite pacotes UDP e TCP de entrada na porta 53 (DNS).
- Permite que o servidor receba e envie consultas DNS.

## 9. Permitir tráfego SMTP e IMAP de entrada para e-mail

```
iptables -A INPUT -i eth0 -p tcp --dport 465 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 587 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 995 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 143 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 993 -j ACCEPT
```
- Permite pacotes TCP de entrada nas portas 465, 587 (SMTP) e 995, 143, 993 (IMAP).
- Permite que o servidor receba e-mails via SMTP e IMAP.

## 10. Restringir o acesso ao banco de dados

```
iptables -A INPUT -s 192.168.2.8 -p tcp --dport 5432 -j ACCEPT
```
- Permite pacotes TCP de entrada na porta 5432 (PostgreSQL) apenas do IP 192.168.2.8.
- Restringe o acesso ao banco de dados PostgreSQL ao servidor de aplicações.

## 11. Restringir o acesso ao Servidor de Aplicações à SubredeLocal

```
iptables -A INPUT -s 192.168.3.0/24 -j ACCEPT
```
- Permite pacotes de entrada da subrede 192.168.3.0/24.
- Permite que dispositivos da subrede local acessem o servidor de aplicações.

## 12. Registrar tentativas de conexões bloqueadas para análise e monitoramento de segurança

```
iptables -A INPUT -j LOG --log-prefix "IPTables-Rejected: " --log-level 4
```
- Registra todas as tentativas de conexão bloqueadas com o prefixo "IPTables-Rejected:" e nível de log 4 (informativo).
- Monitora e analisa tentativas de conexão rejeitadas.

## 13. Bloquear todo o restante do tráfego de entrada

```
iptables -A INPUT -j DROP
```
- Bloqueia todos os pacotes de entrada que não foram explicitamente permitidos.
- Garante que apenas o tráfego autorizado possa acessar o servidor.
