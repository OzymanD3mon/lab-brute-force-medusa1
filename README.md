```markdown
# 🛡️ Laboratório de Auditoria de Autenticação: Força Bruta e Mitigação

Este repositório documenta um laboratório prático de cibersegurança focado na simulação de ataques de força bruta contra serviços de rede (FTP, SMB e Web). O projeto foi construído utilizando o **Kali Linux**, com as ferramentas **Medusa** e **Hydra**, visando o ambiente vulnerável **Metasploitable 2** (incluindo o DVWA).

## 🎯 Objetivos
- Compreender a mecânica de ataques de força bruta e password spraying.
- Utilizar ferramentas de linha de comando para auditar credenciais em diferentes protocolos.
- Identificar falhas de configuração e propor medidas de mitigação (Blue Team).
- Desenvolver habilidades de *troubleshooting* na identificação de falsos positivos.

## 🖥️ Topologia do Ambiente
- **Atacante:** Kali Linux (IP: `192.168.56.102`)
- **Alvo:** Metasploitable 2 (IP: `192.168.56.101`)
- **Rede:** VirtualBox (Isolada em Host-Only)

---

## 🚀 Execução dos Testes e Evidências

### 1. Ataque em FTP (Porta 21) com Medusa
O protocolo FTP foi testado utilizando wordlists customizadas (`user.txt` e `password.txt`).

```bash
medusa -h 192.168.56.101 -U user.txt -P password.txt -M ftp
```

**Resultado:** Credenciais obtidas com sucesso.

![Ataque FTP](images/documentacao%20github.jpg)

### 2. Ataque em SMB (Porta 139/445) com Medusa
Simulação de um ataque direcionado a um usuário específico (`msfadmin`) para auditar o compartilhamento de arquivos.

```bash
medusa -h 192.168.56.101 -u msfadmin -P password.txt -M smbnt
```

**Resultado:** Acesso validado.

![Ataque SMB](images/documentacao%20github%202.jpg)

### 3. Ataque Web Form (DVWA) com Hydra e Troubleshooting
Durante o teste na aplicação web (DVWA), o módulo `web-form` do Medusa apresentou instabilidade. Em uma adaptação à realidade do pentest, a ferramenta foi substituída pelo **Hydra**.

![Instabilidade Medusa](images/Documentacao%20github3.jpg)

**O Falso Positivo (Método GET):**
A primeira execução retornou sucesso para *todas* as senhas.

```bash
hydra -L user.txt -P password.txt 192.168.56.101 http-get-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
```

*Análise:* O formulário do DVWA espera dados via `POST`. Ao usar `http-get-form`, o servidor não processou o login, retornando a página padrão. Como a string de falha não apareceu, o Hydra acusou "falso positivo".

![Hydra GET Falso Positivo](images/Documentacao%20github%204.jpg)

**A Correção (Método POST):**
Ajustando o módulo para `http-post-form`, o ataque funcionou perfeitamente, isolando a credencial correta (`admin:password`).

```bash
hydra -L user.txt -P password.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
```

![Hydra POST Sucesso](images/Documentacao%20github%205.jpg)

---

## 🔒 Recomendações de Mitigação (Blue Team)
Para proteger ambientes contra as técnicas demonstradas acima, recomendo as seguintes implementações:
1. **Rate Limiting e Fail2Ban:** Limitar o número de tentativas de login por IP, banindo temporariamente aqueles que excederem o limite.
2. **Account Lockout:** Bloquear a conta do usuário após um número predeterminado de falhas (ex: 5 tentativas).
3. **MFA (Múltiplos Fatores de Autenticação):** Essencial para proteger o acesso mesmo que a senha seja comprometida.
4. **Políticas de Senhas Fortes:** Invalidar o uso de credenciais previsíveis presentes em wordlists comuns.

---
*Aviso Legal: Projeto estritamente educacional realizado em ambiente isolado.*
