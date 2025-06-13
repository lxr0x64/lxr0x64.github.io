---
layout: post
title: "Checklist de Recon básico pra Bug Bounty"
date: 2025-06-13
categories: [bugbounty, recon]
tags: [recon, bugbounty, hacking, wordlists, ferramentas]
---


<div style="max-width: 100%; text-align: center; margin: 2rem 0;">
  <img src="/assets/img/checklist-recon-bugbounty/banner.png" 
       alt="Banner de Reconhecimento para Bug Bounty" 
       style="width: 100%; max-width: 1200px; height: auto; border-radius: 8px; box-shadow: 0 4px 8px rgba(0,0,0,0.1);" />
</div>

Se você está começando no **Bug Bounty** e se sente perdido sobre por onde começar a explorar um alvo, relaxa!  
Esta checklist é um **guia prático** para te orientar na fase de **reconhecimento (recon)**, a etapa mais crucial para encontrar vulnerabilidades.

> 🧠 *“Recon é o processo de coletar **o máximo de informações possível** sobre o alvo antes de partir para os testes ativos.”*

---

## 🔎 1. Mapeamento de Subdomínios

Subdomínios como `api.site.com`, `dev.site.com` ou `beta.site.com` podem estar desatualizados, mal configurados ou até esquecidos, tornando-os alvos perfeitos para vulnerabilidades.

### Ferramentas e Técnicas:
- **Amass**: Combina busca ativa (bruteforce) e passiva (OSINT).
  ```bash
  amass enum -d site.com -o subdomains.txt
  ```
- **Subfinder**: Rápido, foca em fontes passivas.
  ```bash
  subfinder -d site.com -silent -o subdomains.txt
  ```
- **crt.sh**: Consulta certificados SSL manualmente.
  ```
  https://crt.sh/?q=%.site.com
  ```
- **Dica extra**: Use o `dnsdumpster.com` para mapear subdomínios e visualizar relações DNS.

### Boas Práticas:
- Filtre subdomínios vivos com `httpx`:
  ```bash
  cat subdomains.txt | httpx -silent -o subdomains_alive.txt
  ```
- Priorize subdomínios com status 200 ou 403 para testes manuais.

---

## 🧱 2. Descoberta de Diretórios e Arquivos Ocultos

Diretórios como `/admin`, `/backup.zip` ou `/old/` podem estar expostos e conter informações sensíveis.

### Ferramentas:
- **dirsearch**: Eficiente para brute force de diretórios e arquivos.
  ```bash
  python3 dirsearch.py -u https://site.com -e php,html,js,txt,zip -t 50
  ```
- **gobuster**: Rápido e confiável, ótimo com wordlists personalizadas.
  ```bash
  gobuster dir -u https://site.com -w /path/to/wordlist.txt -t 50 -x php,html,js
  ```
- **ffuf**: Alternativa moderna e altamente customizável.
  ```bash
  ffuf -u https://site.com/FUZZ -w /path/to/wordlist.txt -e .php,.html,.js
  ```

### Dica:
- Use wordlists como `raft-medium-directories.txt` (do projeto SecLists) para maximizar resultados.
- Teste extensões específicas do alvo (ex.: `.asp`, `.aspx` para sites Microsoft).

---

## 🔐 3. Análise de Arquivos JavaScript

Arquivos `.js` podem revelar **endpoints de API**, chaves, tokens ou caminhos internos sensíveis.

### Técnicas:
- **Baixar arquivos JS**:
  ```bash
  wget -r -l2 -H -nd -A js https://site.com -P js_files
  ```
- **Procurar segredos**:
  ```bash
  grep -rEi "apiKey|token|auth|secret|password|key" js_files/
  ```
- **Automatizar com ferramentas**:
  - Use `LinkFinder` para extrair endpoints:
    ```bash
    python3 linkfinder.py -i https://site.com/main.js -o results.html
    ```
  - Use `JSParser` para análise detalhada de JS.

### Dica:
- Procure por comentários em arquivos JS que revelem funcionalidades internas.
- Use `Burp Suite` para mapear endpoints dinamicamente enquanto navega no site.

---

## 🔒 4. Verificação de Configurações de Segurança

Cabeçalhos HTTP e configurações do servidor podem indicar pontos fracos, como ausência de `Content-Security-Policy` ou servidores desatualizados.

### Ferramentas:
- **Checar headers manualmente**:
  ```bash
  curl -I https://site.com
  ```
- **Automatizar com httpx**:
  ```bash
  httpx -u https://site.com -title -status-code -tech-detect -csp-probe
  ```
- **SecurityHeaders.com**: Para análise visual e relatórios detalhados.
  ```
  https://securityheaders.com/?q=site.com
  ```

### O que verificar:
- Cabeçalhos: `X-Frame-Options`, `Content-Security-Policy`, `Strict-Transport-Security`.
- Servidores expostos: Versões de software (ex.: `Apache/2.4.7`).
- Tecnologias usadas: Identifique com `Wappalyzer` ou `WhatWeb`.

---

## 💬 5. Testes de Inputs Básicos (XSS, LFI, SQLi)

Após mapear caminhos e parâmetros, teste payloads simples para vulnerabilidades comuns.

### Exemplos:
- **XSS (Cross-Site Scripting)**:
  ```bash
  https://site.com/search?q=<script>alert('xss')</script>
  ```
  - Payloads adicionais: `<img src=x onerror=alert(1)>`, `"><svg onload=alert(1)>`.
- **LFI (Local File Inclusion)**:
  ```bash
  https://site.com/page?file=../../../../etc/passwd
  ```
  - Teste variações como `../../` ou codificação URL (`%2e%2e%2f`).
- **SQL Injection**:
  ```bash
  https://site.com/id=1' OR '1'='1
  ```
  - Use `sqlmap` para testes automatizados:
    ```bash
    sqlmap -u https://site.com/id=1 --batch
    ```

### Dica:
- Sempre verifique se o parâmetro reflete na resposta do servidor antes de testar payloads.
- Use ferramentas como `Burp Suite` para manipular requisições e testar variações.

---

## 💡 6. Técnicas Avançadas para Subir o Nível

- **Automação de Screenshots**:
  - Use `Gowitness` para capturar imagens de subdomínios vivos:
    ```bash
    gowitness file -f subdomains_alive.txt
    ```
  - Alternativa: `Aquatone` para relatórios visuais detalhados.
- **Pipelines de Recon**:
  - Crie scripts Bash para automatizar o fluxo:
    ```bash
    # Exemplo de pipeline
    subfinder -d site.com -o subdomains.txt
    cat subdomains.txt | httpx -silent | gowitness file -f -
    ```
- **Wordlists Personalizadas**:
  - Gere wordlists baseadas no alvo com `CeWL`:
    ```bash
    cewl https://site.com -w custom_wordlist.txt
    ```

---

## 🚀 Checklist Resumido:

✅ **Subdomínios**: Mapeados e filtrados (vivos).  
✅ **Diretórios/Arquivos**: Brute force concluído, achados documentados.  
✅ **Arquivos JS**: Baixados, analisados e endpoints extraídos.  
✅ **Configurações de Segurança**: Headers e tecnologias verificados.  
✅ **Testes de Input**: Payloads básicos testados (XSS, LFI, SQLi).  
✅ **Anotações**: Tudo documentado com prints e POCs organizados.

---

## 📌 Dica Final

**Recon é o coração do Bug Bounty.** Um recon bem feito te coloca à frente da concorrência, revelando vulnerabilidades que outros não enxergam. Dedique tempo, use ferramentas certas e organize seus achados. O ouro está nos detalhes!