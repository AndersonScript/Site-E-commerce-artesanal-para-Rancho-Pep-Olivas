# Colocando o site do Rancho Pep Olivas para funcionar

Este guia é para quem vai ser o **dono** desta loja online — sem termo técnico, um passo de cada vez.
No fim, o site vai guardar os pedidos, os produtos e o estoque de verdade, e você acessa isso de qualquer celular ou computador.

**Por que a conta é sua e não de quem fez o site?** Porque é onde ficam guardados os dados do seu negócio
(pedidos, clientes, produtos). É de graça, fica no seu nome, e só você decide quem mais tem acesso.

Separe uns 15 a 20 minutos, sem pressa. Cada passo tem exatamente onde clicar.

---

## ✅ Passo 1 — Criar a conta do banco de dados (Supabase)

> **Importante:** use o **seu próprio e-mail** (o do dono do rancho) em tudo neste guia — tanto aqui quanto no
> Passo 7 (Vercel). Assim a loja fica só na sua conta, sem depender de mais ninguém para sempre.

1. Vá em **supabase.com** e clique em **Start your project**.
2. Entre com o **seu** e-mail (ou conta do Google — o que for mais fácil para você).
3. Clique em **New project**.
   - **Name**: pode ser `rancho-pep-olivas`.
   - **Database Password**: crie uma senha forte e **anote em algum lugar seguro** (você não vai precisar digitar de novo no dia a dia, mas é bom guardar).
   - **Region**: escolha a opção mais perto do Brasil (geralmente `South America (São Paulo)`).
4. Clique em **Create new project** e espere uns 2 minutos, enquanto ele é criado.

---

## ✅ Passo 2 — Preparar o banco (copiar e colar, uma vez só)

1. No menu do lado esquerdo, clique no ícone de banco de dados escrito **SQL Editor**.
2. Clique em **New query**.
3. Abra o arquivo **`supabase-schema.sql`** (está junto com este guia), aperte **Ctrl+A** (selecionar tudo) e **Ctrl+C** (copiar).
4. Cole (**Ctrl+V**) tudo dentro da caixa branca do site.
5. Clique no botão verde **Run** (ou aperte Ctrl+Enter).
6. Deve aparecer "Success" (sucesso) embaixo. Pronto — o banco está criado.

> Não precisa entender o que está escrito ali. É só copiar, colar e clicar em Run, uma vez só.

---

## ✅ Passo 3 — Deixar mais seguro (2 cliques)

1. No menu esquerdo, vá em **Authentication** → **Sign In / Providers** → clique em **Email**.
2. Confira se **Confirm email** está **ligado** (verde).
3. Desligue **Allow new users to sign up** (assim ninguém consegue criar login sozinho no seu site — só quem você convidar).
4. Clique em **Save**.

---

## ✅ Passo 4 — Criar o seu próprio login de administrador

1. Ainda em **Authentication**, clique em **Users** → **Add user** → **Invite user**.
2. Digite o **seu e-mail** (o que você vai usar para entrar no painel do site) e confirme.
3. Abra sua caixa de e-mail — vai chegar uma mensagem do Supabase. Clique no link e crie sua senha.
   - **Isso conta como "confirmar o e-mail"** — é exatamente essa confirmação que o site exige na primeira vez que alguém tenta ser administrador.
4. Volte para o site do Supabase, vá em **SQL Editor** → **New query**, cole o texto abaixo (trocando pelo *seu* e-mail e nome) e clique em **Run**:
   ```sql
   insert into public.admins (user_id, nome, email)
   select id, 'Seu Nome', email from auth.users where email = 'seu@email.com';
   ```

Pronto — agora você já é administrador da loja.

---

## ✅ Passo 5 — Ligar o site ao banco

1. No menu esquerdo do Supabase, vá em **Project Settings** (ícone de engrenagem) → **API**.
2. Você vai ver dois campos. Copie os dois:
   - **Project URL** (começa com `https://` e termina em `.supabase.co`)
   - **anon public** (uma chave de letras e números — clique no ícone de copiar)
3. Abra o arquivo **`index.html`** com o Bloco de Notas (ou clique com o botão direito → Abrir com → Bloco de Notas).
4. Aperte **Ctrl+F**, digite `SUPABASE_URL` e aperte Enter — ele vai te levar direto para o lugar certo.
5. Você vai ver duas linhas assim:
   ```
   var SUPABASE_URL = '';
   var SUPABASE_ANON_KEY = '';
   ```
6. Cole cada valor **entre as aspas simples**, ficando assim (com os seus valores, claro):
   ```
   var SUPABASE_URL = 'https://xxxxxxxx.supabase.co';
   var SUPABASE_ANON_KEY = 'eyJhbGciOiJI...';
   ```
7. Salve o arquivo (**Ctrl+S**).

> **Nunca** copie o campo `service_role` (é outra chave, mais perigosa, que não deve ir para o site). Use só a `anon public`.

---

## ✅ Passo 6 — Conferir se funcionou

1. Dê **dois cliques** no arquivo `index.html` para abrir no navegador.
2. Vá até o final da página e clique em **Painel administrativo**.
3. Entre com o e-mail e a senha que você criou no Passo 4.
4. Se abrir o painel — está tudo funcionando! Vá em **Configurações** e coloque o WhatsApp e a chave PIX reais,
   e em **Produtos**, cadastre os produtos de verdade com foto e preço.

Se aparecer alguma mensagem de erro, tire um print da tela e mande para quem preparou o site — com a mensagem exata,
é rápido de resolver.

---

## ✅ Passo 7 — Colocar o site no ar (publicar)

Aqui também: crie as contas abaixo com o **seu próprio e-mail**, não o de quem te ajudou a montar o site.
São gratuitas e ficam 100% no seu nome.

1. Vá em **github.com**, crie uma conta grátis e um repositório novo (pode chamar `rancho-pep-olivas`).
2. Suba os arquivos deste pacote (`index.html`, `README.md` etc.) — o próprio site do GitHub tem um botão
   **uploading an existing file** para arrastar os arquivos, sem precisar saber nada de linha de comando.
3. Vá em **vercel.com**, clique em **Continue with GitHub** (usando a conta do GitHub que você acabou de criar)
   e depois em **Add New → Project**, escolhendo o repositório que você criou.
4. Em **Framework Preset**, deixe **Other** e clique em **Deploy**. Em menos de 1 minuto você recebe um
   endereço `algumacoisa.vercel.app` — esse é o link da sua loja.
5. Volte ao Supabase, em **Authentication → URL Configuration**, e cole esse endereço em **Site URL** (isso faz
   os e-mails de convite e de "esqueci minha senha" funcionarem direito).

> Peça para quem te ajudou a montar o site para acompanhar esta parte com você, se travar em algum passo — mas
> as contas e o link final ficam só seus.

---

## Convidando mais gente para administrar

Depois de estar tudo funcionando: repita o **Passo 4** com o e-mail da nova pessoa, e então vá no seu site,
em **Painel → Equipe**, e adicione o nome e e-mail dela lá. Ela só consegue entrar depois de confirmar o
e-mail dela também — o site não deixa ninguém entrar sem essa confirmação.
