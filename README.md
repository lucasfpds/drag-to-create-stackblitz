# Drag to Create Layout

> Área onde o usuário arrasta e solta componentes para montar um layout livre estilo construtor de páginas.

## Tecnologias

- **Vue 3** com `<script setup>` (JavaScript)
- **Vite** como bundler
- **HTML5 Drag & Drop** nativo (`draggable="true"`)
- **Pointer Events** para mover/redimensionar elementos
- **CSS puro** com grade 8px via `background-image` (gradientes)
- Snap à grade (`Math.round(x / 8) * 8`)

## Requisitos

- Node.js 18+

## Como executar

```bash
npm install
npm run dev
```

Abra http://localhost:5173 no navegador.

## Scripts

| Script | Descrição |
| --- | --- |
| `npm run dev` | Servidor de desenvolvimento (Vite) |
| `npm run build` | Build de produção |
| `npm run preview` | Serve o build de produção |

## Estrutura

```
src/
  main.js              entry: cria o app e importa o CSS
  App.vue              paleta, canvas, drop, seleção, deletar, limpar
  components/
    CanvasElement.vue  renderiza elemento por tipo + handles de seleção/resize
  styles.css           paleta, canvas, grade, elementos, handles
```

### Funcionalidades

- Sidebar com 5 tipos: botão, texto, imagem, input, card (`draggable="true"`)
- Canvas à direita com fundo quadriculado (grade 8px) e highlight de dropzone no `dragover`
- Drop cria elemento na posição do ponteiro com snap à grade
- Elementos em `position: absolute` + `transform: translate(x, y)`
- Seleção por clique (outline + handles); clique no vazio desseleciona; Esc também
- Mover: arrastar o elemento selecionado (pointer events + snap à grade)
- Redimensionar: handle no canto inferior-direito (mín. 48×32)
- Editar: duplo clique no texto/botão/input → `contenteditable` inline; imagem → trocar URL
- Deletar: Delete/Backspace ou ✕ do selecionado
- "Limpar tudo" em 2 passos (clicar → confirmar)


## Rodando no StackBlitz

Basta importar o repositório e rodar normalmente:

```
https://stackblitz.com/github/lucasfpds/drag-to-create-stackblitz
```

O projeto não depende de binários nativos — Vite é JavaScript puro, então
sobe sem bindings nativos no WebContainer do StackBlitz.
