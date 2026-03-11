# TAGGED — Landing Page

> *"Cogito, testimonium, ago — ergo liber sum."*

Landing page oficial do **Tagged**, a Primeira RealTech do Mundo.  
Arquivo HTML único, sem dependências, sem framework — deploy instantâneo.

---

## 🔥 Sobre o Projeto

O Tagged é um app mobile que transforma percepção em pressão sobre a realidade.  
Não é uma rede social. Não é uma plataforma de denúncia. É uma **RealTech** — categoria inédita.

Esta landing page apresenta o produto, sua filosofia e o Manifesto Fundacional completo.

---

## 🗂️ Estrutura

```
tagged-landing/
├── index.html      # Arquivo único — toda a aplicação está aqui
├── vercel.json     # Config de deploy estático
└── README.md
```

### O que está dentro do `index.html`

| Seção | Descrição |
|---|---|
| Hero | Chamada principal + botões de download e manifesto |
| Filosofia | Descartes 1637 × Tagged 2025 — a cadeia lógica |
| Problema | Os 5 mecanismos de apagamento existencial |
| Solução | Os 3 pilares: Consciência, Agência, Consequência |
| Como Funciona | Seção interativa com 4 etapas animadas |
| Terminal | Interface técnica com TypeScript real |
| RealTech | Comparativo de categorias tech |
| Manifesto | Preview animado + acesso ao tratado completo |
| Parcerias | Canal de contato para parceiros |
| Download | CTA final com modal de instalação do APK |
| **Manifesto Completo** | Tratado filosófico com IX Partes — overlay fullscreen |

---

## ⚙️ Tecnologia

- **HTML5 + CSS3 + JavaScript** — zero dependências externas
- **Google Fonts** — DM Serif Display, Space Mono, Syne
- Sistema de views com overlay `position: fixed` — sem framework
- SVGs embutidos em base64 — sem requisições externas
- Modal de download com detecção de dispositivo (Android / Desktop)
- Barra de progresso de leitura no manifesto
- Animações via CSS `@keyframes` e IntersectionObserver
- Background mesh diagonal "USE TAGGED" em CSS puro

---

## 🚀 Deploy

### Vercel (recomendado)

```bash
# 1. Clone o repo
git clone https://github.com/SEU_USER/tagged-landing.git
cd tagged-landing

# 2. Deploy
npx vercel
```

A Vercel detecta HTML estático automaticamente. Em ~30 segundos está no ar.

### Local

Basta abrir o `index.html` no navegador — não precisa de servidor.

```bash
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

---

## 📦 Atualizar o APK

A URL do APK está na seção `#baixar` do `index.html`.  
Para atualizar, substitua o valor do `href` nos dois botões de download:

```html
href="SUA_NOVA_URL_DO_APK"
```

---

## 📬 Contato / Parcerias

**erlingsjunior@gmail.com**

---

## 📄 Licença

Projeto proprietário. Todos os direitos reservados.  
© 2025 Tagged. Planet Earth, Era Digital.
