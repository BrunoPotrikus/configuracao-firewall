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

<br></br>

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

<br></br>

## 3. Permitir tráfego de loopback

```
iptables -A INPUT -i lo -j ACCEPT
```
- Permite todo o tráfego na interface de loopback (`lo`), que é usada para comunicação interna no sistema.
- Garante que serviços internos no sistema possam se comunicar.

<br></br>

## 4. Permitir tráfego de entrada relacionado e estabelecido

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
- Permite pacotes de entrada que fazem parte de conexões já estabelecidas ou relacionadas.
- Mantém a continuidade de conexões legítimas iniciadas pelo sistema.

<br></br>

## 5. Permitir todo o tráfego de saída para a Internet

```
iptables -A OUTPUT -o eth0 -j ACCEPT
```
- Permite todo o tráfego de saída na interface de rede `eth0`.
- Permite que o sistema envie dados para a Internet.
