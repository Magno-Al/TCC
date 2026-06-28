# CLAUDE.md

Guia do projeto para o Claude Code. Este repositório é o **texto de um TCC** (artigo
acadêmico em LaTeX), não um projeto de software. As instruções abaixo orientam a
compilação, as convenções de escrita e o estado atual do documento.

## Visão geral do trabalho

- **Título:** Sistema Embarcado para Monitoramento e Predição de Deterioração Clínica
  Intra-hospitalar.
- **Curso/instituição:** Engenharia de Computação, PUC Minas (Belo Horizonte).
- **Formato:** artigo no template da revista **Abakós** (PUC Minas), `article` 12pt A4.
- **Autores:** 6 alunos + orientador (Felipe Augusto Lara Soares, docente). Definidos em
  [autores.tex](autores.tex).
- **Estágio:** proposta/projeto — Resultados e Conclusão ainda não escritos.

### Tema (resumo técnico)

Dispositivo de beira de leito que monitora sinais vitais continuamente, calcula o escore
**NEWS2** na borda (*edge computing*) e **prediz** o NEWS2 futuro via ML.

- **Plataforma:** Raspberry Pi 3B+ com Linux embarcado gerado por **Buildroot**.
- **Sensores:** MAX30102 (PPG → FC, SpO2, FR, PA) e DS18B20 (temperatura, 1-Wire).
- **Periféricos:** display Waveshare 7", buzzer, LED RGB.
- **Software embarcado:** C++ (drivers + cálculo NEWS2) e interface gráfica em **Qt**.
- **ML:** Regressão Logística (baseline), XGBoost e LSTM, em **Python**. Dataset público
  PhysioNet/Reyna (Sepsis Challenge, ~40.336 pacientes).

## Como compilar

**Motor:** `pdflatex` + `bibtex` (NÃO use biber — o `abntex2cite` usa bibtex).

Ciclo completo (necessário sempre que citações mudam):

```
pdflatex → bibtex → pdflatex → pdflatex
```

Ou, preferencialmente:

```bash
latexmk -pdf principal.tex     # resolve as passadas e o bibtex sozinho
latexmk -C principal.tex       # limpa artefatos de build
```

- **Arquivo principal:** [principal.tex](principal.tex).
- No VS Code, a extensão **LaTeX Workshop** já está configurada em
  [.vscode/settings.json](.vscode/settings.json) com a receita `latexmk` como padrão.
- Sucesso = PDF gerado. O log é verboso; "erro" só é fatal quando aparece
  `Fatal error ... no output PDF file produced`.

### Dependências de pacotes (importante)

O projeto **empacota localmente** vários arquivos do abnTeX2 que normalmente o Overleaf
fornece: `abakos.sty` (+`abakos.def`), `abntex2cite.sty`, `abntex2-alf.bst`,
`abntex2abrev.sty`, `abntex2-options.bib`. **Não apague esses arquivos** — sem eles a
compilação local quebra com "file not found".

Pacote do sistema necessário (TeX Live do Debian): `algorithm2e`, fornecido por
**`texlive-science`** (`sudo apt-get install texlive-science`). Usado em
[abakos.sty](abakos.sty#L53).

## Estrutura de arquivos

```
principal.tex              # raiz: preâmbulo mínimo + \input de tudo
capa.tex                   # cabeçalho/capa do artigo (logo PUC, título, autores)
autores.tex                # \newcommand com nomes/emails dos autores
abakos.sty / abakos.def    # template Abakós (layout, seções, rodapé) — bundled
abntex2*.{sty,bst,bib}     # pacotes ABNT bundled
bibliografia.bib           # 47 entradas (28 @article, @manual, @misc, @standard...)
figuras/                   # .png (fluxograma do método, logo PUC)
capitulos/
  0_resumos/0_resumo_template.tex      # PLACEHOLDER (abstract PT/EN não escrito)
  1_introducao/1_introducao.tex
  2_fundamentacao/2_fundamentacao.tex
  3_correlatos/3_correlatos.tex        # + Tabela 1 (comparação correlatos)
  4_metodologia/
    4_metodologia.tex                  # orquestra: \input materiais + metodo
    4.1_materiais.tex                  # ATIVO (versão condensada dos 6 subtópicos)
    4.1.1_plataforma.tex ... 4.1.6_ferramentas.tex   # COMENTADOS (versões detalhadas)
    4.2_metodo.tex                     # etapas HW/SE/MP do método
  5_resultados/5_resultados.tex        # só o título (a escrever)
  6_conclusao/6_conclusao.tex          # só o título "Cronograma e Considerações Finais"
```

**Atenção a duas versões do mesmo conteúdo:** em [4_metodologia.tex](capitulos/4_metodologia/4_metodologia.tex#L11)
só estão ativos `4.1_materiais` (resumo) e `4.2_metodo`. Os arquivos `4.1.1`–`4.1.6` são
versões **detalhadas** dos mesmos materiais, atualmente `\input` comentados. Ao editar
"materiais", confirme em qual versão o usuário quer mexer.

## Convenções de escrita

- **Citações** (abntex2cite, padrão autor-data):
  - `\cite{chave}` → indireta, dentro do parêntese: (AUTOR, ano).
  - `\citeonline{chave}` → narrativa, autor fora do parêntese: Autor (ano).
  - Atalhos do template: `\citeAutorAno{}`, `\citeCitacao{}`.
- **Idioma:** `babel` brazilian (corpo) + english (abstract em inglês via
  `\selectlanguage{english}`).
- **Siglas:** introduzir como `Nome Completo (SIGLA, do inglês \textit{Termo})`.
- **Subscrito/sobrescrito:** o `inputenc` é utf8, mas **NÃO** suporta caracteres unicode
  de sub/sobrescrito (`₂`, `²`). Use sempre `SpO\textsubscript{2}`, `O\textsubscript{2}`,
  `I\textsuperscript{2}C`. (Caracteres unicode soltos só são inofensivos dentro de
  comentários `%`.)
- **Tabelas:** flutuante `[H]` (pacote `float`), montadas à mão; fonte sempre
  `\textbf{Fonte: ...}` ao final.
- **Figuras:** em `figuras/`, incluídas com `\includegraphics`.
- **Marcadores de revisão no texto:**
  - `\textcolor{red}{Nome: comentário}` → nota de revisão inline (ex.: comentários do
    orientador). NÃO é conteúdo final.
  - Grandes blocos `% ...` de prosa → rascunhos/versões alternativas mantidos para
    referência. Não são conteúdo ativo.

## Estado atual (o que falta)

- ✅ Escritos: Introdução, Fundamentação, Correlatos (com tabela), Materiais, Método.
- ⏳ Pendentes / placeholders:
  - Resumo/Abstract ([0_resumo_template.tex](capitulos/0_resumos/0_resumo_template.tex) —
    ainda é o texto-modelo; e está comentado em [principal.tex:11](principal.tex#L11)).
  - **Resultados** ([5_resultados.tex](capitulos/5_resultados/5_resultados.tex)) — só título.
  - **Conclusão/Cronograma** ([6_conclusao.tex](capitulos/6_conclusao/6_conclusao.tex)) — só título.
  - Etapa **HW01** em [4.2_metodo.tex:13](capitulos/4_metodologia/4.2_metodo.tex#L13) — vazia.

## Armadilhas conhecidas (não-fatais)

- `iec60601` é uma entrada `@standard` que o `abntex2-alf.bst` não conhece → gera um
  warning do bibtex, mas a referência sai. Inofensivo.
- O comando `\editorial` (rodapé, [abakos.sty:200-202](abakos.sty#L200)) fica **indefinido**
  porque sua definição está comentada em [capa.tex:4](capa.tex#L4). Isso causa
  "Undefined control sequence" em todo rodapé (pdflatex retorna código 1, mas gera o PDF).
  Para silenciar: definir `\newcommand{\editorial}{}` ou descomentar/personalizar o rodapé.
