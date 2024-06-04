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
iptables -A FORWARD -i lo -j ACCEPT
```
- Permite todo o tráfego na interface de loopback (`lo`), que é usada para comunicação interna no sistema.
- Garante que serviços internos no sistema possam se comunicar.

## 4. Permitir tráfego de entrada relacionado e estabelecido

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT
```
- Permite pacotes de entrada que fazem parte de conexões já estabelecidas ou relacionadas.
- Mantém a continuidade de conexões legítimas iniciadas pelo sistema.

## 5. Permitir Tráfego entre Rede Local e DMZ

```
iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
```
- Permite tráfego livre entre a rede local (interface `eth0`) e a DMZ (interface `eth1`).

## 6. Bloquear Acesso Direto da Internet para a SubredeLocal

```
iptables -A FORWARD -i eth1 -o eth0 -m state --state NEW -j DROP
```
- Bloqueia novas conexões iniciadas da Internet (interface `eth1`) para a SubredeLocal (interface `eth0`), melhorando a segurança contra acessos não autorizados.

## 7. Bloquear todo o tráfego de entrada da Internet que não seja uma resposta a solicitações iniciadas internamente

```
iptables -A INPUT -i eth0 -m state --state NEW -j DROP
```
- Bloqueia novos pacotes de entrada na interface `eth0` que não sejam respostas a solicitações iniciadas internamente.
- Previne conexões não solicitadas da Internet.

## 8. Permitir conexões HTTP e HTTPS de entrada da Internet

```
iptables -A INPUT -i eth0 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 443 -j ACCEPT
```
- Permite pacotes TCP de entrada nas portas 80 (HTTP) e 443 (HTTPS).
- Permite que o servidor receba tráfego web.

## 9. Permitir Tráfego de Saída para Serviços de E-mail

```
iptables -A FORWARD -p tcp -s 192.168.3.3 -o eth0 -m tcp --dport 465 -j ACCEPT
iptables -A FORWARD -p tcp -s 192.168.3.3 -o eth0 -m tcp --dport 587 -j ACCEPT
iptables -A FORWARD -p tcp -s 192.168.3.3 -o eth0 -m tcp --dport 995 -j ACCEPT
iptables -A FORWARD -p tcp -s 192.168.3.3 -o eth0 -m tcp --dport 143 -j ACCEPT
iptables -A FORWARD -p tcp -s 192.168.3.3 -o eth0 -m tcp --dport 993 -j ACCEPT
```
- Permite que a estação de trabalho com IP `192.168.3.3` se conecte a serviços de e-mail, incluindo SMTP, IMAP e POP3 nas portas especificadas.

## 10. Acesso Restrito ao Banco de Dados

```
iptables -A FORWARD -s 192.168.2.8 -o eth0 -p tcp --dport 5432 -j ACCEPT
```
- Permite que apenas o servidor de aplicações com IP `192.168.2.8` acesse o banco de dados PostgreSQL na porta 5432.

## 11. Restringir Acesso às Portas 80 e 443 na Subrede Local

```
iptables -A FORWARD -i eth1 -p tcp -m tcp --dport 80 -j ACCEPT
iptables -A FORWARD -i eth1 -p tcp -m tcp --dport 443 -j ACCEPT
```
- Permite tráfego para portas HTTP (80) e HTTPS (443) na subrede local.

## 12. Acesso ao Servidor de Aplicações

```
iptables -A FORWARD -s 192.168.3.0/24 -d 192.168.2.8 -j ACCEPT
```
- Permite pacotes de entrada da subrede 192.168.3.0/24.
- Permite que dispositivos da subrede local acessem o servidor de aplicações.

## 13. Registrar tentativas de conexões bloqueadas para análise e monitoramento de segurança

```
iptables -A INPUT -j LOG --log-prefix "IPTables-Rejected: " --log-level 4
```
- Registra todas as tentativas de conexão bloqueadas com o prefixo "IPTables-Rejected:" e nível de log 4 (informativo).
- Monitora e analisa tentativas de conexão rejeitadas.

## 14. Bloquear todo o restante do tráfego de entrada

```
iptables -A INPUT -j DROP
```
- Bloqueia todos os pacotes de entrada que não foram explicitamente permitidos.
- Garante que apenas o tráfego autorizado possa acessar o servidor.

