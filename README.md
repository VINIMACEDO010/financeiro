# fin. — Controle Financeiro Pessoal

> PWA de finanças pessoais com autenticação, banco de dados em nuvem e suporte offline. Funciona no iPhone via Safari e em qualquer navegador desktop.

---

## ✨ Funcionalidades

- **Dashboard** com saldo do mês, receitas, despesas e barras por categoria
- **Relatórios** com gráfico donut de distribuição e top 5 maiores gastos
- **Lançamentos** com suporte a crédito, débito e Pix
- **Parcelamento automático** — divide o valor em N meses com um clique
- **Categorias customizadas** — adicione e gerencie suas próprias categorias
- **Swipe para excluir** no mobile (arrastar para a esquerda)
- **Backup e restauração** via arquivo `.json`
- **Login por e-mail e senha** — cada usuário vê só seus próprios dados
- **Sessão persistente** — abre direto no app nas próximas visitas
- **Instalável no iPhone** via Safari → Adicionar à Tela de Início (PWA)
- **Layout responsivo** — sidebar no desktop, navegação inferior no mobile

---

## 🛠️ Tecnologias

| Camada | Tecnologia |
|---|---|
| Front-end | HTML + CSS + JavaScript (arquivo único) |
| Banco de dados | [Supabase](https://supabase.com) (PostgreSQL) |
| Autenticação | Supabase Auth |
| Hospedagem | GitHub Pages |

Sem frameworks, sem build tools, sem dependências locais.

---

## 🚀 Como usar (fork e configuração)

### 1. Crie um projeto no Supabase

Acesse [supabase.com](https://supabase.com), crie uma conta e um novo projeto.

### 2. Execute o SQL para criar as tabelas

No **SQL Editor** do painel Supabase, rode:

```sql
CREATE TABLE IF NOT EXISTS transactions (
  id TEXT PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE NOT NULL,
  data DATE NOT NULL,
  mes TEXT NOT NULL,
  descricao TEXT NOT NULL,
  tipo TEXT NOT NULL,
  categoria TEXT NOT NULL,
  receita NUMERIC(12,2) DEFAULT 0,
  despesa NUMERIC(12,2) DEFAULT 0,
  pagamento TEXT DEFAULT 'debito',
  parcelas INTEGER DEFAULT 1,
  parcela_atual INTEGER DEFAULT 1,
  grupo_id TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

ALTER TABLE transactions ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Usuário vê só suas transações"
ON transactions FOR ALL
USING (auth.uid() = user_id)
WITH CHECK (auth.uid() = user_id);

CREATE TABLE IF NOT EXISTS custom_categories (
  id SERIAL PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE NOT NULL,
  nome TEXT NOT NULL,
  tipo TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, nome)
);

ALTER TABLE custom_categories ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Usuário vê só suas categorias"
ON custom_categories FOR ALL
USING (auth.uid() = user_id)
WITH CHECK (auth.uid() = user_id);
```

### 3. Configure as credenciais no código

Em `index.html`, localize as duas linhas no topo do `<script>` e preencha com os valores do seu projeto (**Settings → API** no painel Supabase):

```js
const SUPABASE_URL = 'https://SEU_PROJETO.supabase.co';
const SUPABASE_KEY = 'SUA_ANON_KEY';
```

> Use apenas a chave **anon/public** (`sb_publishable_...`). Nunca coloque a chave secreta no código.

### 4. Publique no GitHub Pages

1. Crie um repositório público
2. Faça upload do `index.html` (renomeado como `index.html`)
3. Vá em **Settings → Pages → Deploy from branch → main**
4. Acesse `https://SEU_USUARIO.github.io/REPOSITORIO/`

---

## 📱 Instalar no iPhone

1. Abra a URL no **Safari**
2. Toque em **Compartilhar** (ícone de seta)
3. Selecione **Adicionar à Tela de Início**
4. O app abre em tela cheia, sem barra do navegador

---

## 🔒 Segurança dos dados

- Cada usuário acessa **apenas seus próprios dados** via Row Level Security (RLS) no Supabase
- A chave `anon` no código é projetada para ficar exposta — a segurança real vem das políticas RLS
- Repositório público **não compromete** os dados dos usuários

---

## 📦 Estrutura

```
/
└── index.html   # App completo — HTML, CSS e JS em arquivo único
└── README.md
```

---

## 📄 Licença

Uso pessoal. Sinta-se livre para adaptar para seu próprio controle financeiro.
