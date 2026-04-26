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
