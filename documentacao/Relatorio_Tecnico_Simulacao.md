# Relatório Técnico de Simulação de Ataques de Força Bruta

## 1. Configuração do Ambiente de Laboratório

Para a execução dos testes, foi configurado um ambiente isolado utilizando duas Máquinas Virtuais (VMs) no VirtualBox:

| VM | Sistema Operacional | Função | Endereço IP (Exemplo) |
| :--- | :--- | :--- | :--- |
| **Atacante** | Kali Linux (Versão 202x.x) | Plataforma de testes de penetração. | 192.168.56.101 |
| **Alvo** | Metasploitable 2 | Ambiente vulnerável com diversos serviços. | 192.168.56.102 |
| **Alvo Web** | DVWA (Instalado no Metasploitable 2 ou VM separada) | Aplicação web intencionalmente vulnerável. | 192.168.56.102 (Porta 80) |

**Configuração de Rede:** As VMs foram configuradas com uma rede **Host-Only** (ou Rede Interna) para garantir que o tráfego de ataque permaneça isolado da rede externa.

## 2. Cenário 1: Força Bruta em Serviço FTP (Metasploitable 2)

### 2.1. Enumeração Inicial

Antes do ataque, é crucial identificar o serviço e a porta.

**Comando de Exemplo (Nmap):**
\`\`\`bash
nmap -sV 192.168.56.102 -p 21
\`\`\`

**Resultado Esperado:** O Nmap deve identificar o serviço **vsftpd 2.3.4** rodando na porta 21.

### 2.2. Execução do Ataque (Medusa)

O Medusa será utilizado para tentar diversas combinações de usuário e senha contra o serviço FTP.

**Wordlists Utilizadas:**
- Usuários: \`../wordlists/usuarios_simples.txt\`
- Senhas: \`../wordlists/passwords_simples.txt\`

**Comando de Exemplo (Medusa):**
\`\`\`bash
medusa -H 192.168.56.102 -u msfadmin -P ../wordlists/passwords_simples.txt -M ftp
\`\`\`
*Nota: Para um ataque mais abrangente, use a opção \`-U\` para lista de usuários.*

**Validação de Acesso:**
\`\`\`bash
ftp 192.168.56.102
# user: msfadmin
# pass: msfadmin
\`\`\`

### 2.3. Recomendações de Mitigação

| Risco | Mitigação Recomendada |
| :--- | :--- |
| **Credenciais Fracas** | Impor políticas de senhas fortes (complexidade, tamanho mínimo de 12 caracteres). |
| **Ausência de Bloqueio** | Implementar mecanismos de bloqueio de conta após um número limitado de tentativas falhas (ex: 3 a 5). |
| **Protocolo Inseguro** | Desabilitar o FTP e migrar para SFTP ou FTPS, que criptografam as credenciais e o tráfego. |
| **Software Desatualizado** | Atualizar o serviço FTP para uma versão mais recente e segura. |

## 3. Cenário 2: Força Bruta em Formulário Web (DVWA)

*Nota: Este cenário geralmente requer ferramentas como Burp Suite ou scripts Python (com a biblioteca `requests`) para automatizar o envio de requisições HTTP POST, pois o Medusa não é a ferramenta ideal para formulários web que exigem gerenciamento de sessão (cookies).*

### 3.1. Preparação (Análise de Requisição)

Utilizar o Burp Suite para interceptar a requisição de login do DVWA e identificar os parâmetros de envio (username, password, user\_token).

### 3.2. Execução do Ataque (Exemplo com Hydra)

Embora o desafio mencione Medusa, o Hydra é mais comumente usado para ataques web que não exigem JavaScript.

**Comando de Exemplo (Hydra):**
\`\`\`bash
# Exemplo simplificado, o comando real exige o path completo do formulário e o token de sessão
hydra -L ../wordlists/usuarios_simples.txt -P ../wordlists/passwords_simples.txt 192.168.56.102 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:S=Location"
\`\`\`

### 3.3. Recomendações de Mitigação

| Risco | Mitigação Recomendada |
| :--- | :--- |
| **Automação Simples** | Implementar **CAPTCHA** ou mecanismos de verificação humana após falhas. |
| **Ausência de Limite** | Aplicar **Rate Limiting** (limitação de taxa) no servidor web para restringir o número de requisições por IP em um período de tempo. |
| **Token de Sessão** | Utilizar tokens CSRF (Cross-Site Request Forgery) e garantir que o token seja validado e expire corretamente. |

## 4. Cenário 3: Password Spraying em Serviço SMB (Metasploitable 2)

O *password spraying* tenta uma **única senha comum** contra **muitos usuários** para evitar o bloqueio de contas.

### 4.1. Enumeração de Usuários

O Metasploitable 2 não é ideal para enumeração de usuários SMB, mas em um ambiente real, ferramentas como `enum4linux` ou `nmap` (com script `smb-enum-users`) seriam usadas.

**Comando de Exemplo (Nmap):**
\`\`\`bash
nmap --script smb-enum-users -p 445 192.168.56.102
\`\`\`

### 4.2. Execução do Ataque (Medusa)

Usaremos uma lista de usuários e uma única senha comum (ex: "password").

**Wordlists Utilizadas:**
- Usuários: \`../wordlists/usuarios_simples.txt\`
- Senha Única: \`password\`

**Comando de Exemplo (Medusa):**
\`\`\`bash
medusa -H 192.168.56.102 -U ../wordlists/usuarios_simples.txt -p password -M smb
\`\`\`

### 4.3. Recomendações de Mitigação

| Risco | Mitigação Recomendada |
| :--- | :--- |
| **Password Spraying** | Implementar **detecção de anomalias** que identifique múltiplas falhas de login em **diferentes contas** vindas do **mesmo IP**. |
| **Senhas Comuns** | Proibir o uso de senhas fracas ou comuns através de filtros de senhas. |
| **Autenticação** | Implementar **Autenticação Multifator (MFA)** para acesso a serviços críticos. |
| **Enumeração** | Desabilitar a enumeração de usuários anônimos no serviço SMB. |

---
*Documento preparado por Manus AI.*

## 5. Referências

[1] Kali Linux Official Website: https://www.kali.org/
[2] Medusa Brute Force Tool Documentation: https://www.kali.org/tools/medusa/
[3] Metasploitable 2 Documentation: https://docs.rapid7.com/metasploit/metasploitable-2/
[4] Damn Vulnerable Web Application (DVWA) Official Site: http://www.dvwa.co.uk/
[5] OWASP Cheat Sheet Series - Authentication: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
