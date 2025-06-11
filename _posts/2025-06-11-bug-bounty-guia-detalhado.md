---
layout: post
title: "Bug Bounty: Como começar do zero (com dicas reais e práticas)"
date: 2025-06-11
categories: bug bounty
tags: ["bug bounty", "seguranca-web", "hacking ético", "guia completo"]
---

# Bug Bounty: Como começar do zero (com dicas reais e práticas)

Quer entrar no mundo do Bug Bounty, mas não sabe nem por onde começar?  
Relaxa, esse guia é feito pra você — explicando **passo a passo**, com dicas práticas, atalhos certos (sem pular aprendizado) e o **mindset real** que vai te ajudar a encontrar vulnerabilidades de verdade.

---

## 🧠 Entendendo o que é Bug Bounty

**Bug Bounty** é quando empresas oferecem **recompensas em dinheiro** (ou reconhecimento) pra quem encontra **falhas de segurança** nos sistemas delas — de forma legal e controlada.  
Você age como um "atacante ético", testando aplicações web, APIs, apps mobile, e até infra cloud.

---

## 🧩 O que você precisa pra começar?

- Vontade de aprender
- Entender como sistemas funcionam
- Saber onde e como procurar bugs
- Saber reportar com clareza

**Não precisa:**
- Ter faculdade
- Ser fluente em inglês (mas ajuda)
- Ser programador avançado

---

## 📚 Etapas do zero ao seu primeiro bug

### 1. Fundamentos web

Antes de tudo, entenda como funciona a web:

✅ Estude:
- HTTP: métodos, status, headers, cookies, sessions
- HTML, JavaScript e o DOM
- Como APIs funcionam (REST, GraphQL)
- Como login, autenticação e tokens funcionam

💡 Dicas:
- Use o DevTools do navegador todo dia
- Faça interceptação com Burp Suite pra ver requisições de verdade
- Brinque com `curl`, `httpie` no terminal

---

### 2. Vulnerabilidades comuns (OWASP Top 10)

Você **precisa** dominar isso. Aqui estão as mais comuns em Bug Bounty:

| Falha | Impacto | Como estudar |
|-------|---------|--------------|
| XSS | Executa JS na vítima | Web Security Academy (Reflected + Stored) |
| SQLi | Manipula o banco de dados | SQLi labs, TryHackMe |
| IDOR | Acessa dados de outro usuário | Exploração manual em APIs |
| CSRF | Ações sem consentimento | Testes com contas diferentes |
| SSRF | Servidor acessa URLs internas | Testar uploads por URL |
| LFI/RFI | Lê ou executa arquivos no servidor | Testar `../../etc/passwd` |
| Open Redirect | Redireciona para phishing | `?next=https://malicioso.com` |

---

### 3. Reconhecimento (Recon)

O segredo do Bug Bounty é achar **onde ninguém tá olhando**.

🧰 Ferramentas:
- `subfinder`, `amass` → descobrem subdomínios
- `httpx`, `nmap` → verifica quais estão vivos e com portas abertas
- `ffuf`, `dirsearch` → força descoberta de caminhos escondidos
- `waybackurls`, `gau`, `katana` → acha endpoints antigos

📌 Dica: monte seu **próprio script de recon** automatizado. Automatize a coleta, mas revise tudo manualmente.

---

### 4. Treinamento realista

Treinar é onde você ganha reflexo e confiança.

🏋️ Plataformas gratuitas:
- [PortSwigger Academy](https://portswigger.net/web-security)
- [TryHackMe](https://tryhackme.com/)
- [HackTheBox](https://hackthebox.com/)
- [Root Me](https://www.root-me.org)

🎯 Prática específica:
- Resolva labs de cada tipo de falha
- Tente bypassar filtros
- Documente o que você aprendeu

---

### 5. Participando de programas reais

Depois de praticar:

🛡 Plataformas de Bug Bounty:
- [HackerOne](https://hackerone.com)
- [Bugcrowd](https://bugcrowd.com)
- [Intigriti](https://intigriti.com)
- [YesWeHack](https://yeswehack.com)

📄 Leia o escopo com atenção:
- O que pode ser testado?
- Qual impacto é aceito?
- Como o sistema trata bugs duplicados?

Dica: comece com **programas novos**, **menos famosos** — menos concorrência, mais chances.

---

### 6. Ferramentas essenciais (pra quem tá começando)

Você **não precisa ser tool master**. Aprenda a usar bem poucas:

| Ferramenta | Função |
|------------|--------|
| Burp Suite | Interceptar e modificar requisições |
| curl/httpie | Testar endpoints via terminal |
| ffuf | Fuzzing de parâmetros e diretórios |
| nmap | Descobrir serviços e portas |
| subfinder | Descobrir subdomínios |
| nuclei | Scanner de vulnerabilidades baseado em templates |

---

### 7. Como encontrar bugs de verdade

🧠 Dica: **Pare de pensar como usuário. Comece a pensar como atacante.**

➡️ Perguntas que você deve se fazer sempre:
- O que aconteceria se eu mudasse esse ID?
- Esse campo está validando entrada?
- Consigo burlar esse fluxo?
- E se eu repetir esse request várias vezes?
- E se eu remover esse header?
- Tem confiança demais no lado do cliente?

---

### 8. Como fazer um bom report

Escreva como se fosse um **documento técnico objetivo**, não como um textão.

📄 Estrutura:
```
Título: tipo da falha + endpoint afetado
Resumo: o que é, onde está, qual impacto
Reprodução: passo a passo claro
Impacto: o que um atacante pode fazer
POC: payload, script, print, link
```

💡 Evite:
- Reports vagos ("tem XSS aí")
- Falta de contexto ("eu vi isso no console")
- Linguagem agressiva ou arrogante

---

### 9. Como evoluir de verdade

📌 Rotina ideal:
- 70% estudo/prática
- 30% caça real em programas

🧠 Estude por falha, não por bounty
- Escolha uma falha (ex: XSS)
- Estude teoria, depois pratique, depois tente caçar

🤝 Participe da comunidade:
- Twitter (segue hunters)
- Discords de segurança
- Escreva no GitHub ou blog (reforça o que você aprende)

---

## 🧠 Mentalidade certa

Bug Bounty é:
- Um jogo de paciência
- Um jogo de observação
- Um jogo de repetição

Você vai errar, reportar duplicado, perder sono…  
Mas vai aprender mais do que qualquer curso ensina.

---

## 🏁 Conclusão

Começar no Bug Bounty é difícil — mas **muito possível**.  
Você não precisa ser gênio. Só precisa ser persistente.

Treine. Quebre coisas. Aprenda com os erros.  
Quando sair seu primeiro bug válido, você vai ver que valeu a pena.

E se precisar de ajuda no caminho, já sabe: esse blog é sua base. Vamo junto.

---

> “A diferença entre quem acha bug e quem não acha? O primeiro tentou mais vezes.”  
> — lxr0x64

