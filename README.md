# Petsalut — Landing page "Direto"

Página de vendas de 8 dobras: Meta Ads → `index.html` → checkout do plano → `obrigado.html`.

## Arquivos

- `index.html` — landing page (HTML/CSS/JS em um arquivo só)
- `obrigado.html` — página pós-checkout (dispara `purchase` no dataLayer)
- `assets/` — logo, hero-bg.jpg (hero), hero-pets.jpg (dobra 3), indicacao-dog.jpg (indicação), rede-mapa.jpg (rede credenciada), tutor.jpg (sobre), depo-1/2/3.jpg (depoimentos)

## O que precisa ser preenchido antes de publicar

Tudo fica no bloco `CONFIG` no fim de cada arquivo. Campo vazio = elemento fica oculto na página.

### `index.html`

| Campo | O que é |
|---|---|
| `checkout.total_care` / `plus_care` / `premium_care` | Já apontam para `https://conta.petsalut.com/contratar` com `?plano=...&plan_id=...`. Ver seção "Checkout" abaixo. |
| `regulamento` | Link do regulamento completo (tabela de planos, FAQ e rodapé). |
| `rede` | Link da lista/mapa da rede credenciada. |
| `privacidade` | Link da política de privacidade. |
| `empresa` | Razão social e CNPJ do rodapé. |
| `faq.multipets` / `pagamento` / `poscompra` / `elegibilidade` | Respostas das 4 perguntas pendentes do FAQ. |
| `stat` | Número de destaque na seção "Sobre" (ex.: `+ de 15k` consultas realizadas, como no site). Vazio = oculto. |
| `depoimentos` | Lista de `{ texto, nome, pet, cidade }`. Vazia = seção de depoimentos oculta. |
| `whatsapp` | `5527998024119` (27 99802-4119), confirmado. `endereco` e `contatos` vieram do site petsalut.com. |

### `obrigado.html`

| Campo | O que é |
|---|---|
| `confirmacao` | Por onde chega a confirmação (e-mail / WhatsApp). |
| `carteirinha` | Como e quando chega a carteirinha / nº de cadastro. |
| `ativacao` | Prazo de ativação do plano. |
| `indicacao` | Mecânica do programa de indicação. |

O checkout deve redirecionar para `obrigado.html?plano=plus_care&valor=169.90` (valores: `total_care`, `plus_care`, `premium_care`).

## Checkout (conta.petsalut.com/contratar)

O checkout é único: o plano é escolhido dentro dele (botões `.plan` com `data-value`). Os IDs são:

| Plano | `plan_id` |
|---|---|
| Total Care | 15 |
| Plus Care | 9 |
| Premium Care | 10 |

Hoje a página **não lê nenhum parâmetro da URL** para pré-selecionar o plano (testado com `?plan=`, `?plano=`, `?plan_id=`; o `Register.lead({plan:null})` vem fixo do servidor). Os 3 botões da LP levam para o mesmo checkout, e o `?plano=` serve só de rastreio até o dev implementar a pré-seleção.

Para o dev do checkout: no `register.js`, dentro de `Register.lead`, logo depois do bloco `$('.plan').click(...)`, basta:

```js
var pre = new URLSearchParams(window.location.search).get('plan_id');
if (pre && $('#formPet .plan[data-value="' + pre + '"]').length) {
  $('#formPet .plan[data-value="' + pre + '"]').trigger('click');
}
```

Com isso os links da LP já passam a abrir o checkout com o plano marcado, sem mudar nada na LP.

## Dados a confirmar com o cliente

1. **Preço do Plus Care**: briefing traz R$ 1.629,90; site atual traz R$ 169,90. A página usa R$ 169,90.
2. **Tabela de cobertura e carências**: extraída do site atual (petsalut.com). Validar que está vigente.
3. **Lista de cidades**: briefing tem um item em branco. Página usa as 9 cidades do mapa enviado pelo cliente: Vitória, Vila Velha, Serra, Cariacica, Guarapari, Cachoeiro de Itapemirim, Colatina, Linhares e São Mateus.
4. **Foto `assets/tutor.jpg`**: confirmar quem é e autorização de uso de imagem.
5. **"Sexta à noite" no H1**: horário publicado de consulta é seg–sex 7h–20h / sáb 7h–14h. Confirmar se a urgência atende fora disso; se não, trocar por "quando precisar".
6. **Selo "Mais queridinho"** no Plus Care: é o texto usado no site atual; confirmar se continua valendo.
7. **Depoimentos**: 3 vídeos do canal oficial da Petsalut no YouTube (Shorts), com thumbnail local e player carregado só no clique.
8. **Domínio / hospedagem** e **Pixel Meta** (configurar no GTM).

## Eventos no dataLayer (para o GTM)

| Evento | Quando | Parâmetros |
|---|---|---|
| `view_planos` | tabela de planos entra na tela | — |
| `click_cta_scroll` | qualquer botão de CTA fora da tabela | `dobra` (header, 1, 3, 5, 6, 8) |
| `click_escolher_plano` | botão de um plano na tabela | `plano`, `valor` |
| `click_whatsapp` | botões do WhatsApp (abaixo dos planos e no FAQ) | — |
| `play_depoimento` | clique em um depoimento em vídeo | `video` (id do YouTube) |
| `purchase` | carregamento de `obrigado.html` | `plano`, `valor`, `moeda` |

UTMs da URL da LP são repassados automaticamente para a URL do checkout.
