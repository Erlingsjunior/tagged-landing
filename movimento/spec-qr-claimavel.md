# TAGGED — Especificação Técnica: Sistema de QR Claimável
### Adesivos físicos com QR "virgem" que o usuário vincula à própria denúncia

---

## Conceito

O adesivo é impresso ANTES de qualquer tag existir. Cada um carrega um código
único e aleatório. O comprador cola, abre sua denúncia no app, escaneia o
próprio adesivo e o vincula. A partir daí, qualquer pessoa que escanear cai
direto na denúncia. Digital e físico, ao mesmo tempo.

---

## 1. O código

- Formato: `apptagged.com/q/{codigo}` — código de 10 caracteres, alfabeto
  sem ambiguidade (sem 0/O, 1/l/I): ex. `K7XM-2QF9-BD`
- Geração: aleatória criptográfica (nanoid/UUID truncado), NUNCA sequencial
  — sequencial permite "chutar" códigos e sequestrar adesivos alheios.
- Espaço: 30^10 ≈ 590 trilhões de combinações. Colisão em lote: verificada
  na geração contra o banco.
- Lotes: gerar em blocos (ex. 10.000) com `batch_id` pra rastrear qual
  cartela/gráfica/pedido originou cada código.

## 2. Modelo de dados (Firestore)

```
colecao: qr_codes
doc id: {codigo}
{
  status: "virgem" | "vinculado" | "bloqueado",
  batch_id: "lote-2026-08-A",
  tag_id: null | "abc123",          // denúncia vinculada
  claimed_by: null | uid,            // dono do vínculo (nunca exposto publicamente)
  claimed_at: null | timestamp,
  scans: 0                           // contador de leituras (métrica de rua!)
}
```

## 3. O resolvedor — Cloud Function em `/q/{codigo}`

```
GET /q/{codigo}
  ├─ não existe  → página 404 amigável + CTA baixar app
  ├─ virgem      → página "Este adesivo ainda não tem causa.
  │                Baixe a Tagged e ative o seu." + botão APK/loja
  │                (CADA SCAN DE ADESIVO VIRGEM = FUNIL DE AQUISIÇÃO)
  └─ vinculado   → incrementa scans; redireciona pra /t/{tag_id}
                   (página pública da denúncia: título, status, assinaturas,
                    botão "Assinar no app" + deep link se instalado)
```

## 4. Fluxo de vínculo no app

1. Usuário abre a própria denúncia → botão **"Vincular adesivo físico"**.
2. Abre a câmera com scanner de QR (exige o adesivo em mãos = prova de posse).
3. App lê o código e chama `claimQr(codigo, tagId)`:
   - Transação atômica: só vincula se `status == "virgem"` (primeiro que
     escaneia, leva — sem corrida).
   - Grava tag_id, claimed_by, claimed_at; status = "vinculado".
4. Confirmação visual: "Adesivo ativado! Quem escanear cai na sua denúncia."
5. **Imutável**: QR vinculado não re-vincula. (Exceção opcional: o próprio
   dono pode re-apontar para OUTRA denúncia dele, nunca de terceiros.)

## 5. Regras de segurança

- Vínculo somente autenticado e somente via scanner (não aceitar código
  digitado — dificulta claim remoto de adesivo que a pessoa não possui).
- O QR SEMPRE aponta pra denúncia, NUNCA pro perfil — proteção física de
  quem denuncia (especialmente tags anônimas: a página pública mostra
  "representada pela Tagged").
- Rate-limit no resolvedor e no claim (contra enumeração e abuso).
- `status: "bloqueado"` para moderação (adesivo usado pra spam/golpe).

## 6. Impressão (dados variáveis)

- Print-on-demand com "impressão de dados variáveis" (VDP): cada adesivo da
  cartela recebe QR único — serviço padrão em gráficas digitais.
- Pipeline: script gera lote no banco → exporta PDF da cartela com os QRs
  posicionados no template do adesivo (eu já tenho o gerador de QR + o
  template visual; é juntar os dois num PDF por cartela).
- QR com correção de erro nível H (30%) — sobrevive a chuva, riscos e
  desbotamento parcial.
- Teste de leitura: imprimir 1 cartela piloto e escanear todos antes do lote.

## 7. Métricas que esse sistema destrava

- `scans` por adesivo = calor de rua por região (mapa de onde o movimento
  está fisicamente ativo!)
- Conversão adesivo→download (scans de virgem que viraram instalação)
- Assinaturas originadas por QR vs. orgânicas no app
- Ranking de "adesivos mais escaneados" = gamificação futura (selo pra quem
  colou o adesivo mais escaneado do mês?)

## 8. Ordem de implementação sugerida

1. Rota pública `/t/{tagId}` (página web da denúncia) — pré-requisito.
2. Coleção qr_codes + Cloud Function do resolvedor `/q/`.
3. Scanner + claim no app (botão na denúncia).
4. Script de geração de lote + PDF de impressão.
5. Cartela piloto na gráfica → teste → produção.
