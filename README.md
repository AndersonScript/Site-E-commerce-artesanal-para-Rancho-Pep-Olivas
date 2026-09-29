# Rancho Pep Olivas — loja de vinhos e azeites

> **Se você é o dono do rancho e não é da área técnica:** use o arquivo `GUIA-DO-DONO.md` em vez deste — é o mesmo conteúdo, em passo a passo simples, sem termos técnicos.

Site em **um único arquivo** (`index.html`) + banco de dados no **Supabase** (plano gratuito).
Pedidos, estoque, produtos, cupons, administradores e configurações ficam salvos online — o que o cliente
compra aparece no seu painel, de qualquer aparelho.

## O que tem
**Loja:** catálogo com busca e ordenação · carrinho · checkout com **busca de CEP** (ViaCEP) · frete fixo, grátis acima de X ou "a combinar" ·
cupons de desconto · PIX copia-e-cola + QR Code · resumo do pedido no WhatsApp · **acompanhar pedido** (número + celular) ·
portão "tenho 18 anos" · política de privacidade · faixa de avisos · assistente de perguntas frequentes (opcional).

**Painel (`/#admin`):** visão geral com vendas dos últimos 7 dias, ticket médio, mais vendidos e alerta de estoque baixo · kanban de pedidos
(recebido → confirmado → separação → embalagem → enviado → entregue) com romaneio imprimível, exportação CSV e aviso de novo pedido ·
produtos com foto (envio direto do aparelho) · cupons · **aparência** (cores, logo, textos) · configurações · equipe (administradores com **e-mail verificado**).

## Passo a passo (uma vez só, ~15 minutos)

### 1. Criar o banco
1. Entre em **supabase.com**, crie uma conta e um **New project** (escolha a região *São Paulo*; anote a senha do banco).
2. Menu **SQL Editor → New query**. Cole **todo** o conteúdo de `supabase-schema.sql` e clique **Run**. (Pode rodar de novo sem duplicar nada.)

### 2. Ajustar o login (Authentication)
- **Sign In / Providers → Email:** deixe **Confirm email** ligado (é ele que verifica o e-mail dos administradores) e **desligue "Allow new users to sign up"** — assim ninguém se cadastra sozinho.
- **URL Configuration:** em *Site URL* coloque o endereço do seu site (ex.: `https://ranchopepolivas.vercel.app`) e adicione o mesmo endereço em *Redirect URLs*.
  Isso faz os links de convite e de "esqueci minha senha" voltarem para o seu site.

### 3. Criar o primeiro administrador (você)
1. **Authentication → Users → Invite user** com o seu e-mail. Abra o e-mail, clique no link e crie sua senha (isso verifica o e-mail).
2. Volte ao **SQL Editor** e rode (trocando o e-mail e o nome):
   ```sql
   insert into public.admins (user_id, nome, email)
   select id, 'Seu Nome', email from auth.users where email = 'seu@email.com';
   ```

### 4. Ligar o site ao banco
1. **Project Settings → API**: copie a **Project URL** e a chave **anon public** (ou *publishable*).
2. Abra `index.html` num editor de texto, procure por `SUPABASE_URL` (no começo do script) e cole os dois valores entre as aspas.
   > A chave "anon" é pública por natureza. A proteção está nas regras do banco (Row Level Security), já criadas pelo SQL. **Nunca** cole a chave `service_role`.

### 5. Primeiro acesso
Abra o site → `/#admin` → entre com seu e-mail e senha. Vá em **Configurações** e coloque o **WhatsApp**, a **chave PIX** e o frete;
em **Aparência**, a logo e os textos; em **Produtos**, as fotos e os preços reais.

### 6. Publicar
- **GitHub:** crie um repositório e envie estes arquivos (pode arrastar pelo site do GitHub).
- **Vercel:** vercel.com → *Add New → Project* → escolha o repositório → *Framework: Other* → **Deploy**.
  O `vercel.json` já aplica os cabeçalhos de segurança. Depois, coloque o endereço final em *Site URL* (passo 2).

## Adicionar mais administradores
Painel → **Equipe**. Convide a pessoa no Supabase (*Authentication → Users → Invite user*); quando ela aceitar o convite e criar a senha,
informe o e-mail dela na aba Equipe. O sistema **recusa e-mails ainda não verificados**, e mesmo que alguém esteja na lista, sem e-mail verificado não tem acesso.

## Cuidados
- **Plano gratuito do Supabase:** projetos sem uso podem ser pausados após um tempo (confira as regras atuais no painel deles). Se pausar, é só clicar em *Restore*. Os dados não se perdem.
- **E-mails de convite/senha:** o envio padrão do Supabase tem limite baixo por hora. Se precisar de mais, configure um SMTP próprio (Project Settings → Auth → SMTP).
- **Backup:** exporte os pedidos em CSV de vez em quando (painel → Visão geral ou Pedidos).
- **Pagamento:** o PIX é o "copia e cola" com a **sua** chave; a confirmação é manual (botão *Confirmar pagamento* no kanban). Não há cobrança automática de cartão.
- **Pedidos falsos:** qualquer visitante pode criar um pedido (é uma loja pública). O servidor confere preço e estoque, mas não há limite de pedidos por pessoa; se sofrer spam, dá para adicionar um captcha.
- **Biblioteca externa:** o site carrega o `supabase-js` (versão fixa) de `cdn.jsdelivr.net`.

## Como isto foi testado
- O SQL foi executado em um PostgreSQL real (com o ambiente do Supabase simulado): regras de segurança por perfil, preço/cupom/frete/estoque calculados no servidor,
  cancelamento devolvendo estoque, e-mail verificado para administradores, acompanhamento de pedido — 51 verificações.
- O site foi testado de ponta a ponta simulando cliente e administrador contra um Supabase simulado — 65+ verificações.
- **Não** foi testado contra um projeto Supabase real (não há acesso a ele daqui). Siga o passo 5 como conferência e, se algo falhar, o console do navegador (F12) mostra a mensagem do servidor.
