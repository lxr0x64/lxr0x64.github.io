---
layout: post
title: "As 10 vulnerabilidades mais exploradas (OWASP Top 10)"
date: 2025-06-09 18:00:00 -0300
categories: segurança-web
tags: [owasp, segurança, bugbounty, hacking, pentest]
---

# As 10 vulnerabilidades mais exploradas (OWASP Top 10)

Se você quer entrar no mundo do hacking ou já tá no corre com Bug Bounty, entender o **OWASP Top 10** é tipo aprender o ABC do jogo. Esse ranking mostra as vulnerabilidades de segurança mais comuns (e mais exploradas) em aplicações web. Bora ver uma por uma, com exemplos e dicas de como identificar?

---

## 1. **Broken Access Control** (Controle de acesso quebrado)
**O que é:** Quando usuários conseguem acessar dados ou funções que não deviam.

**Exemplo clássico:** Um usuário comum acessa `/admin` e ganha painel de admin.
**Outro exemplo:** Alterar seu ID para acessar info de outro usuário: `/user/123` → `/user/124`.

**Casos reais:** Muitas APIs não fazem a checagem correta do dono do recurso. Se você manda um PUT em `/api/v1/profile/444` e a API atualiza, mesmo você sendo o 123, tá quebrado.

**Dica prática:**
- Testa trocar seu ID ou o de outros usuários.
- Remove headers como Authorization e tenta acessar recursos.
- Verifica se botões e funções "admin" aparecem em usuários comuns (às vezes o front esconde, mas o backend permite).

---

## 2. **Cryptographic Failures** (Falhas criptográficas)
**O que é:** Erros em como dados sensíveis são protegidos ou nem são.

**Exemplo clássico:** Senhas salvas em texto puro. É comum em apps pequenos ou antigos.
**Outro exemplo:** JWTs com algoritmo `none`, ou sem assinatura válida.

**Casos reais:** Alguns sites retornam número de cartão inteiro, CPF completo ou tokens em URLs (grave!).

**Dica prática:**
- Procura dados sensíveis em respostas da API ou no HTML.
- Analisa cookies e tokens (usa jwt.io) e verifica se têm assinatura.
- Procura headers tipo `Set-Cookie: session=...; HttpOnly; Secure` se não tiver isso, já acende alerta.

---

## 3. **Injection** (Injeção)
**O que é:** Quando dados inseridos pelo usuário viram código tipo SQL, comandos, ou até JavaScript.

**Exemplo clássico:** `' OR 1=1 --` num login burlar o acesso.
**Outro exemplo:** `<script>alert(1)</script>` num campo que é refletido na página (XSS).

**Casos reais:**
- Formulários de busca ou login sem sanitização.
- Filtros de produtos que aceitam parâmetros diretamente na query SQL.

**Dica prática:**
- Testa `'`, `"`, `--`, `;`, `<script>`, `<img src=x onerror=alert(1)>`.
- Observa erros SQL, ou comportamento estranho no DOM.
- Usa ferramentas tipo Burp ou até curl pra testar payloads diretamente.

---

## 4. **Insecure Design** (Design inseguro)
**O que é:** Quando a falha está no *jeito* como o sistema foi pensado não é bug, é burrada.

**Exemplo:** Sistema de login sem limite de tentativas, você pode tentar infinitas senhas.
**Outro exemplo:** Geração de cupons ilimitados sem validação.

**Casos reais:** Sites que deixam fazer reset de senha só com o e-mail, sem token ou verificação.

**Dica prática:**
- Abusa dos fluxos. Cria cenários como: repetir envio de requisição, modificar valor de cupom, repetir cancelamento, etc.
- Tenta "encaixar" seu próprio fluxo pra burlar lógicas do sistema.

---

## 5. **Security Misconfiguration** (Mau uso de configurações de segurança)
**O que é:** Quando a aplicação tá mal configurada tipo debug ligado, headers ausentes, diretórios abertos.

**Exemplo:** Página que mostra erro com stack trace completo.
**Outro exemplo:** `/phpinfo.php` exposto.

**Casos reais:** Muitos apps esquecem debug habilitado (ex: Django, Laravel), e você vê stack de exceção bonitinha.

**Dica prática:**
- Visita URLs tipo `/debug`, `/config`, `/admin`, `/phpinfo`, `/actuator`.
- Usa [securityheaders.com](https://securityheaders.com) e veja se os headers estão certos: `X-Frame-Options`, `Content-Security-Policy`, etc.
- Veja também se diretórios listam arquivos (`index of /uploads`).

---

## 6. **Vulnerable and Outdated Components** (Componentes vulneráveis e desatualizados)
**O que é:** Usar bibliotecas ou sistemas que já têm falhas conhecidas.

**Exemplo:** WordPress com plugin vulnerável; jQuery 1.x com XSS.
**Outro exemplo:** Apache Struts com falha de RCE.

**Casos reais:** A falha do Log4Shell (Log4j) foi explorada em milhões de sistemas só porque a galera não atualizava.

**Dica prática:**
- Olha os JS e CSS carregados e procura no Google versão + CVE.
- Testa vulnerabilidades conhecidas, com prova de conceito.
- Usa `retire.js`, `npm audit`, `yarn audit`, `OWASP Dependency Check`.

---

## 7. **Identification and Authentication Failures** (Falhas de autenticação e identificação)
**O que é:** Falhas na verificação de quem é você login, sessão, reset de senha, etc.

**Exemplo:** Login que aceita `admin:admin` ou não bloqueia tentativas.
**Outro exemplo:** Reset de senha que aceita token vencido.

**Casos reais:** Muitos apps deixam reset de senha com token reutilizável, ou sem tempo de expiração.

**Dica prática:**
- Tenta brute force com senhas simples (mas com cautela, ética sempre).
- Testa fazer login com session de outro usuário.
- Tenta resetar senha mais de uma vez com o mesmo link/token.

---

## 8. **Software and Data Integrity Failures** (Falhas de integridade de software e dados)
**O que é:** Quando o sistema aceita atualizações ou comandos sem validar a origem.

**Exemplo:** CDN carrega script externo sem SRI (Subresource Integrity).
**Outro exemplo:** App aceita upload de firmware sem autenticação.

**Casos reais:** Plugins que baixam scripts ou dependências dinamicamente e executam sem verificação.

**Dica prática:**
- Olha no HTML se scripts externos têm `integrity="..."`.
- Testa fazer upload de arquivos que deveriam ser restritos config.json, firmware.bin, etc.
- Analisa dependências carregadas no runtime.

---

## 9. **Security Logging and Monitoring Failures** (Falta de logs e monitoramento)
**O que é:** Quando o sistema nem percebe que tá sendo atacado.

**Exemplo:** Erros de login repetidos e nenhuma ação do sistema.
**Outro exemplo:** Acesso fora de horário e sem alerta.

**Casos reais:** Sites que não mandam e-mail quando você muda sua senha, ou fazem login em novo IP/localização.

**Dica prática:**
- Tenta ações maliciosas (com cuidado) e veja se algo muda: login errado, vários requests seguidos, etc.
- Testa alterações de e-mail, senha, perfil e veja se o sistema notifica.

---

## 10. **Server-Side Request Forgery (SSRF)**
**O que é:** Fazer o servidor acessar uma URL que VOCÊ mandou e às vezes acessar recursos internos que só ele pode.

**Exemplo:** Upload de imagem via URL, e você manda `http://localhost/admin`.
**Outro exemplo:** Requisição que aceita `file://`, `ftp://`, etc.

**Casos reais:** Muitas aplicações têm SSRF oculto em previews, PDF generators, ou validação de link.

**Dica prática:**
- Tenta usar URLs internas como `http://127.0.0.1`, `http://169.254.169.254` (servidores cloud), `file:///etc/passwd`.
- Monitora se o sistema responde diferente com IPs internos.
- Usa serviços como Burp Collaborator, RequestBin, ou webhook.site pra detectar requisições que saem do servidor.

---

## Conclusão
Aprender essas 10 falhas é tipo ganhar um mapa do tesouro. A maioria dos bugs encontrados em programas de Bug Bounty nascem aqui. Quanto mais você praticar, mais rápido vai identificar essas falhas no mundo real.

Treina sua mente pra pensar como atacante: "e se eu tentar mudar isso aqui?", "o que acontece se eu repetir esse passo?", "tem algo que tá sendo confiado demais no cliente?" esse é o mindset.

Quer um quiz rápido pra validar o que você aprendeu?
