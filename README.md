# Documentação: Técnicas de Enumeração e Ataques de Força Bruta

> Este documento consolida anotações sobre metodologias de ataque, com foco em enumeração de serviços e técnicas de força bruta (Brute Force).  
> **Uso:** apenas em ambientes de teste/laboratório autorizados. Veja a seção **Aviso Legal**.

---

## Sumário
- [1. Conceitos Fundamentais](#1-conceitos-fundamentais)  
  - [Ataque de Força Bruta (Brute Force)](#ataque-de-força-bruta-brute-force)  
  - [Tipos de Ataque (Variações)](#tipos-de-ataque-variações)  
- [2. Componente Chave: Wordlists](#2-componente-chave-wordlists)  
  - [Criação Manual de Wordlists](#criação-manual-de-wordlists)  
  - [Regras de "Mangling"](#regras-de-mangling)  
- [3. Metodologia de Ataque e Simulação](#3-metodologia-de-ataque-e-simulação)  
  - [Etapa 1: Enumeração (Reconhecimento)](#etapa-1-enumeração-reconhecimento)  
  - [Etapa 2: Execução do Ataque de Força Bruta](#etapa-2-execução-do-ataque-de-força-bruta)  
- [4. Ferramentas e Ambiente de Laboratório](#4-ferramentas-e-ambiente-de-laboratório)  
- [Aviso Legal](#aviso-legal)  

---

## 1. Objetivo
Documentar, de forma educativa, conceitos e boas práticas relacionadas à enumeração de serviços e ataques de força bruta. Não contém instruções para uso em alvos não autorizados.

## 2. Conceitos Fundamentais

### Ataque de Força Bruta
Tentar combinações de credenciais até encontrar uma que funcione. Eficácia depende do tamanho/complexidade das senhas e de mecanismos de defesa (lockouts, rate limiting, MFA).

Vantagens: simples e direto.  
Desvantagens: custo computacional elevado e detecção fácil em ambientes protegidos.

### Variações Comuns
- Permutação (todas as combinações possíveis).
- Híbrido (wordlist + mutações).
- Password spraying (poucas senhas testadas em muitas contas).
- Credential stuffing (credenciais vazadas reutilizadas).
*(Nota: O uso de `-e` no `echo` interpreta os caracteres `\n` como novas linhas).*

### Regras de "Mangling"
Técnica usada por ferramentas como o *John the Ripper* para aplicar mutações em palavras de uma wordlist (ex: `senha` -> `S3nh@123`), aumentando a eficácia da lista.

---

## 3. Metodologia de Ataque e Simulação
Uma simulação de ataque típica segue as fases de enumeração e, em seguida, a exploração (força bruta).

### Etapa 1: Enumeração (Reconhecimento)
O objetivo é identificar serviços ativos, portas abertas e configurações no alvo.

* **Teste de Conectividade:**
    ```bash
    ping -c 3 [IP]
    ```

* **Scan de Portas e Serviços (Nmap):**
    ```bash
    nmap -sV -p 21,22,80,139,445 [IP]
    ```
    *(Escaneia os serviços (`-sV`) nas portas comuns: FTP, SSH, HTTP, SMB, HTTPS/SMB).*

* **Enumeração Específica (Ex: SMB):**
    * Listar compartilhamentos SMB com sessão nula:
        ```bash
        smbclient -L //[IP] -U ""
        ```
    * Usar scripts de enumeração detalhada para SMB:
        ```bash
        enum4linux -a [IP] | tee enum4-output.txt
        ```
        *(O `-a` executa todos os testes. `| tee` salva a saída no arquivo `enum4-output.txt` e a exibe no terminal).*

### Etapa 2: Execução do Ataque de Força Bruta
Após identificar serviços abertos (como FTP, SMB, RDP), pode-se tentar um ataque de força bruta usando as wordlists.

* **Ferramenta:** `Medusa` (ou `Hydra`)
* **Cenário de Exemplo (Ataque ao FTP):**
    Conforme anotado, se um serviço FTP for encontrado, será necessário um usuário e senha.
    ```bash
    medusa -h [IP] -U users.txt -P Passwords.txt -M ftp -t 6
    ```
    * `-h [IP]`: O host/alvo.
    * `-U users.txt`: Arquivo com a lista de usuários.
    * `-P Passwords.txt`: Arquivo com a lista de senhas.
    * `-M ftp`: Módulo de serviço a ser atacado (neste caso, FTP).
    * `-t 6`: Número de threads (processos paralelos).

* **Outros Serviços:** A mesma técnica pode ser aplicada a outros serviços, como **RDP** (Remote Desktop Protocol) ou **SMB**.

---

## 4. Ferramentas e Ambiente de Laboratório
Para praticar e executar essas técnicas, as seguintes ferramentas e ambientes são comumente utilizados.

### Ferramentas de Força Bruta
* Hydra
* nCrack
* John the Ripper (JTR)
* Patator
* Medusa

### Ambiente de Laboratório (Prática)
* **Atacante:** Kali Linux (contém todas as ferramentas mencionadas).
* **Vítima:** Metasploitable2 (uma máquina virtual deliberadamente vulnerável para testes).

---

### Aviso Legal

Este material é apenas para fins educacionais e para uso em ambientes controlados e autorizados. Executar varreduras, enumerações ou ataques contra sistemas sem permissão explícita do proprietário é ilegal e antiético. Utilize estas técnicas somente em laboratórios de teste ou com autorização formal.