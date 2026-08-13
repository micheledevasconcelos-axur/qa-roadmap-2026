# QA Delivery Roadmap

Roadmap de entregas da QA, com visualização em Gantt. Editável direto pela página — sem tocar em código, sem planilha manual no Figma, sem arquivo local. Os dados ficam versionados neste repositório (`data.json`) e a página é publicada via GitHub Pages.

## O que este projeto substitui

Antes, o roadmap era montado manualmente no Figma: cada entrega era uma caixa clonada à mão, com texto, datas, link do Teams e avatar digitados dentro. Aqui, cada entrega é um registro em `data.json`; a página (`index.html`) lê esses registros e desenha a timeline automaticamente — posição, largura e cor da barra são calculadas a partir da data de início/fim e da categoria.

## Estrutura do repositório

| Arquivo | Função |
|---|---|
| `index.html` | A página do roadmap (timeline + painel de edição). Não precisa editar para uso normal. |
| `data.json` | A lista de entregas — a fonte de dados real. Pode ser editada pela página ou direto no GitHub. |

## Como publicar (uma vez)

1. Repositório precisa ser **público** (GitHub Pages grátis exige isso, a menos que sua organização tenha plano com Pages privado).
2. `index.html` e `data.json` devem estar na **raiz** do repositório.
3. **Settings → Pages** → Source: **Deploy from a branch** → branch `main`, pasta `/ (root)` → **Save**.
4. Aguarde ~1 minuto. O site fica em `https://SEU_USUARIO.github.io/NOME_DO_REPOSITORIO/`.

A página detecta automaticamente o usuário/repositório pela própria URL — não é necessário editar nada no código para isso funcionar.

## Como visualizar

Basta abrir o link do GitHub Pages. O roadmap carrega os dados mais recentes de `data.json` automaticamente, para qualquer pessoa com o link — sem login e sem instalar nada.

## Como editar

1. Abra o link do GitHub Pages → clique **✏️ Editar entregas**.
2. **+ Nova entrega** para adicionar, ou ✏️/🗑️ em um item da lista para editar/excluir.
3. Clique **💾 Salvar no GitHub**. Na primeira vez, vai pedir um token:
   - GitHub → **Settings → Developer settings → Fine-grained tokens → Generate new token**
   - Repository access: **Only select repositories** → escolha este repositório
   - Permissions → **Contents: Read and write**
   - Gere e cole o token na página. Ele fica salvo só no seu navegador, nunca é commitado.
4. Depois de conectado, **Salvar no GitHub** grava direto (um commit real) e **🔄 Atualizar** busca a versão mais recente.

Cada pessoa que for editar conecta o próprio token, uma vez, no próprio navegador. Quem só visualiza não precisa de nada.

## Conflitos de edição simultânea

Se duas pessoas salvarem quase ao mesmo tempo, a segunda tentativa é rejeitada pela API do GitHub (em vez de sobrescrever silenciosamente). A página avisa para clicar em **Atualizar**, refazer a mudança e salvar novamente.

## Formato de `data.json`

```json
{
  "id": "d1",
  "name": "Nome da entrega",
  "category": "Cyber Threat Intelligence",
  "start": "2025-11-10",
  "end": "2025-11-21",
  "link": "https://teams.microsoft.com/...",
  "status": ["published"],
  "owners": ["MC"]
}
```

- `category`: precisa ser uma das chaves definidas em `CATEGORIES` dentro de `index.html` (mesmas cores da legenda).
- `status`: qualquer combinação de `"published"` (👍 Publicação em #update-deliveries) e `"homolog"` (🌗 Homologação fora do lifecycle).
- `owners`: iniciais do(s) responsável(is) pela QA; cada conjunto de iniciais recebe uma cor automática, consistente em todas as barras.

O arquivo é sempre salvo ordenado por data de início — isso mantém o histórico de commits legível e reduz conflitos de merge.

## Backup manual

O botão **⬇ Baixar backup local (.json)** no painel de edição salva uma cópia de segurança do estado atual, independente do GitHub.

## Limitações conhecidas

- Sem autenticação de usuários — qualquer pessoa com um token de escrita no repositório pode editar. Adequado para um time interno de confiança; não é um controle de acesso granular.
- Sem histórico de "quem editou o quê" além do que o próprio Git já registra nos commits.
- O site publicado pode levar até ~1 minuto para refletir um novo commit.
