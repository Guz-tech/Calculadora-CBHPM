# 🩺 Calculadora CBHPM

Calculadora de valores de procedimentos e exames laboratoriais da **CBHPM** (Classificação Brasileira Hierarquizada de Procedimentos Médicos), com suporte a **várias edições anuais** (2012, 2014, 2016, 2018…), cada uma com sua própria tabela de portes, lista de procedimentos e lista de exames.

É uma aplicação de **arquivo único** (`index.html`), sem servidor, sem instalação e sem dependências externas. Funciona no computador e no celular.

> **Importante:** o projeto **não traz tabelas oficiais da CBHPM**. Os valores de porte que vêm preenchidos são apenas **exemplos**. Você importa as tabelas da sua fonte (AMB, contrato, planilha interna). A CBHPM define os portes, mas os valores em reais variam conforme a referência ou o contrato usado.

---

## Sumário

1. [Funcionalidades](#funcionalidades)
2. [Como usar](#como-usar)
3. [Como o valor é calculado](#como-o-valor-é-calculado)
4. [Formatos de arquivo (CSV)](#formatos-de-arquivo-csv)
5. [Armazenamento e backup](#armazenamento-e-backup)
6. [Dados pré-carregados (dados.json)](#dados-pré-carregados-dadosjson)
7. [Publicação no GitHub Pages](#publicação-no-github-pages)
8. [Estrutura técnica](#estrutura-técnica)
9. [Limitações conhecidas](#limitações-conhecidas)
10. [Aviso legal](#aviso-legal)

---

## Funcionalidades

- **Várias edições da CBHPM** em paralelo, cada uma com nome e **nº da edição definido por você**.
- **Tabela de portes por edição** (1A a 14C), porte anestésico (AN0 a AN8), valor da **UCO** e valor do **filme**.
- **Lista de procedimentos por edição** e **lista separada de exames laboratoriais / patologia clínica por edição**.
- **Cálculo do valor** de cada item, com auxiliares, urgência e redutor de múltiplos procedimentos ou via de acesso.
- **Exames por fração de porte** (ex.: 0,1 × porte 1A): o valor muda sozinho quando você altera o valor do porte na tabela.
- **Importação de CSV** de tabelas, procedimentos e exames; **exportação de CSV** das tabelas.
- **Backup e restauração** de todos os dados em JSON.
- Tema claro e escuro automático.

## Como usar

O app tem cinco abas.

### Calcular
1. Escolha a **Tabela CBHPM** (edição).
2. Escolha a **Lista**: *Procedimentos* ou *Exames laboratoriais / patologia clínica*.
3. Busque por código ou nome. A lista mostra os 60 primeiros resultados com o valor de cada item.
4. Toque em um item para ver o **detalhe** (honorário, auxiliares, anestesia, UCO, filme e total).
5. As opções **Auxiliares**, **Múltiplos / via** e **Urgência** valem para toda a lista.

### Tabelas
Uma tabela por edição da CBHPM.
- Campos: **Nº da edição** (livre), **Nome**, **Valor da UCO (R$)** e **Valor do filme (R$/m²)**.
- A caixa de texto guarda os valores, uma linha por item: `porte;valor` (ex.: `3B;45,20`). Dá para colar direto do Excel.
- Botões: **Salvar**, **Importar CSV**, **Exportar esta (CSV)**, **Exportar todas (CSV)**, **Duplicar versão** e **Excluir**.
- Enquanto uma tabela tiver valores de exemplo, a aba Calcular mostra um aviso amarelo. O aviso some quando você salva ou importa valores seus.

### Procedimentos
Lista de procedimentos **da edição selecionada**. Importe por CSV ou cole na caixa de texto e clique em **Salvar procedimentos**.

### Exames lab.
Mesma lógica da aba Procedimentos, mas para exames laboratoriais / patologia clínica. Aceita também o formato por **frações de porte**.

### Backup
- **Gerar backup:** cria um JSON com tudo (edições, tabelas, procedimentos e exames).
- **Baixar dados.json:** baixa o mesmo conteúdo como arquivo, para publicar junto com o site (veja a próxima seção).
- **Restaurar:** cola o JSON e recupera os dados, no mesmo navegador ou em outro.

### Fluxo recomendado para o primeiro uso
1. **Tabelas:** crie ou renomeie a edição, defina o nº da edição e importe os portes, a UCO e o filme.
2. **Procedimentos:** importe a lista da edição.
3. **Exames lab.:** importe a lista de exames da edição.
4. **Calcular:** confira alguns valores conhecidos.
5. **Backup:** gere um JSON e guarde uma cópia.

## Como o valor é calculado

```
honorário   = valor do porte × fração × redutor × (1 + urgência)
auxiliares  = honorário × percentual dos auxiliares
anestesia   = valor do porte anestésico (ANn)
UCO         = qtd UCO × valor da UCO da edição
filme       = qtd filme × valor do filme da edição (R$/m²)

TOTAL = honorário + auxiliares + anestesia + UCO + filme
```

| Item | Regra |
|---|---|
| **Fração** | Opcional. Se não informada, vale 1. Usada nos exames laboratoriais (ex.: 0,1 do porte 1A). |
| **Redutor** | 100%, 70% ou 50% (múltiplos procedimentos ou via de acesso). Incide só no honorário. |
| **Urgência** | +30% sobre o honorário. |
| **Auxiliares** | 0 a 4. Percentual acumulado sobre o honorário: 30% / 50% / 70% / 90%. |
| **Anestesia** | Coluna em branco = **sem anestesia**. Valor `0` = porte anestésico 0 (AN0). Não sofre redutor nem urgência. |

Se o código de porte de um item não existir na tabela da edição, o detalhe mostra um aviso. Se o item usa UCO ou filme e o valor não foi cadastrado na edição, o aviso também aparece.

## Formatos de arquivo (CSV)

Separador **`;`** (ponto e vírgula) ou tabulação, decimais com **vírgula**. Aceita UTF-8 (com ou sem BOM) e CSV do Excel em Windows-1252. A linha de cabeçalho é ignorada.

### Tabela de portes (aba Tabelas)

```
1A;12,86
2B;67,83
AN0;50,00
UCO;15,00
FILME;20,00
```

- Códigos de porte: `1A` a `14C`. Porte anestésico: `AN0` a `AN8`. Valores da edição: `UCO` e `FILME`.
- A importação **mescla**: valores existentes são atualizados e os novos são adicionados.
- O CSV exportado tem as colunas `edicao;nome;codigo;valor`. Ao importá-lo, cada edição é criada ou atualizada pelo nome e pelo nº da edição, o que permite restaurar várias de uma vez.

### Procedimentos e exames (abas Procedimentos e Exames lab.)

```
codigo;nome;porte;porte anestesico;qtd UCO;qtd filme;fracao
10101012;Consulta em consultório;2C;0;0;0
```

Só código e nome são obrigatórios. A 7ª coluna (fração) é opcional.

### Exames por fração de porte

```
codigo;procedimento;fracao;porte_base
40301010;3-metil histidina, dosagem no soro;0,1;PO1A
```

Linhas cujo 4º campo tem o formato `PO1A`, `PO2B` etc. são reconhecidas automaticamente. Colunas extras (como `valor_porte_base` e `valor`) são ignoradas, porque o valor é recalculado pela tabela da edição.

> Em todos os formatos, importar **mescla** com o que já existe: códigos repetidos são atualizados, códigos novos são adicionados, e nada é apagado.

## Armazenamento e backup

- Os dados ficam no **localStorage do navegador**, na chave `cbhpm`, **por endereço (domínio)** e por navegador.
- Não há envio de dados para nenhum servidor.
- O limite típico do navegador é de cerca de **5 MB por site**. Muitas edições completas da CBHPM podem se aproximar disso, e quando o limite estoura o salvamento falha sem aviso. Teste fechando e reabrindo a página depois de importar tudo.
- Limpar os dados do navegador apaga as tabelas. **Gere backups com frequência.**
- Trocar de endereço (por exemplo, de `github.io` para um domínio próprio) também "perde" os dados: gere o backup antes e restaure no novo endereço.
- Backups antigos que contenham dados de convênios continuam sendo restaurados normalmente. Esses dados são ignorados, porque a funcionalidade foi removida.


## Estrutura técnica

Arquivo único em HTML, CSS e JavaScript puro, sem bibliotecas e sem etapa de build.

### Estado (`S`)

```js
S = {
  ver: 3,
  rev: '2026-10-20T20:00:00.000Z', // carimbo gerado ao baixar o backup; detecta dados novos
  eds: [{
    id: 'e1',
    name: 'CBHPM 2012',
    num: '5',          // nº da edição, texto livre
    ex: 0,             // 1 = tabela com valores de exemplo
    t: '1A;12,86\n…',  // tabela: portes, AN0–AN8, UCO, FILME
    p: 'cod;nome;porte;anest;uco;filme;fracao\n…',  // procedimentos
    l: '…'             // exames laboratoriais (mesmo formato)
  }]
}
```

### Principais funções

| Função | Papel |
|---|---|
| `load()` / `save()` / `mig()` | Lê e grava o `localStorage`; migra dados de versões anteriores. |
| `tbl(texto)` | Converte o texto de uma tabela em mapa `{código: valor}`. |
| `procs(edição, 'p' \| 'l')` | Lê a lista de procedimentos ou de exames da edição. |
| `calcP(edição, mapa, item, opções)` | Calcula honorário, auxiliares, anestesia, UCO, filme e total. |
| `viewCalc` / `wireCalc` / `upd` | Aba Calcular. |
| `viewEd` / `wireEd` | Aba Tabelas (inclui importar e exportar). |
| `viewL(k)` / `wireL(k)` | Abas Procedimentos (`'p'`) e Exames lab. (`'l'`). |
| `importCsv` / `importProcs` / `mergeT` | Importação de CSV com mescla. |
| `exportEd` | Exportação de CSV. |
| `show(aba)` | Navegação entre as abas. |

### Histórico de decisões

- A aba **Convênios** (valor de UCO e de filme por convênio, percentual sobre o porte, inflator e deflator) foi **removida por enquanto**. A calculadora trabalha só com as tabelas da CBHPM, e UCO e filme passaram a ser da própria edição.
- Procedimentos e exames passaram a ser **por edição**, porque o mesmo código pode ter porte, UCO ou filme diferentes de um ano para outro.

## Limitações conhecidas

- Sem login e sem edição compartilhada: cada navegador tem os seus dados. O `dados.json` distribui uma versão única, somente leitura, e não recebe alterações feitas pelos usuários.
- A lista de resultados mostra no máximo 60 itens por vez. Use a busca para filtrar.
- Trocar de edição nas abas Procedimentos e Exames lab. descarta o que foi digitado e não salvo na caixa de texto.
- O arredondamento dos valores é feito só na exibição (2 casas); os cálculos internos usam o valor completo.
- A exportação em CSV cobre as **tabelas de portes**. Procedimentos e exames são salvos pelo backup em JSON.
- O cálculo de auxiliares e do acréscimo de urgência segue uma regra padrão (30% / 50% / 70% / 90% e +30%). Contratos específicos podem exigir regras diferentes.

## Aviso legal

A CBHPM é uma classificação publicada pela Associação Médica Brasileira (AMB). Este projeto é uma ferramenta de cálculo e **não distribui o conteúdo oficial da classificação**. Os valores exibidos dependem inteiramente dos dados que você importa, e a conferência com a tabela oficial ou com o contrato vigente é de sua responsabilidade antes de qualquer uso em cobrança, faturamento ou negociação.
