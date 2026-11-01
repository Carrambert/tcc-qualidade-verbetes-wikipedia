# Qualidade de verbetes da Wikipédia — TCC

Notebooks utilizados para organizar as participações acadêmicas, recuperar métricas das revisões, comparar estados de verbetes e aplicar indicadores quantitativos de qualidade.

O trabalho considera atividades acadêmicas realizadas entre **2018 e 2024**. São **243 participações aptas à consulta**, das quais **177 tiveram contribuições identificadas**, totalizando **1.021 revisões**. A comparação Antes × Depois envolve **70 unidades grupo/verbete**: **49 verbetes preexistentes** e **21 criados nas atividades**.

## Organização

```text
TCC_Wikipedia_GitHub/
├── notebooks/
│   ├── 01_consolidacao_alunos_grupos.ipynb
│   ├── 02_coleta_contribuicoes_mediawiki.ipynb
│   ├── 03_estados_antes_depois.ipynb
│   ├── 04_amostra_artigos_bons_destacados.ipynb
│   ├── 05_calculo_dos_cinco_indicadores.ipynb
│   └── 06_graficos_dos_resultados.ipynb
├── dados/
│   ├── brutos/         # arquivos de entrada
│   └── processados/    # Excel gerados pelos notebooks
├── resultados/
│   └── graficos/       # PNG dos gráficos
├── requirements.txt
└── README.md
```

## O que faz cada notebook

| Notebook | Descrição | Saída principal |
|---|---|---|
| **01 — Consolidação** | Organiza disciplinas, anos, alunos, usuários, grupos e verbetes. Recupera o cadastro corrigido e os registros de auditoria dos cruzamentos. | `base_participacoes_consolidada.xlsx` |
| **02 — Contribuições** | Documenta a extração pela API MediaWiki, as métricas do wikitext e os saldos das revisões acadêmicas. Usa a lista histórica de revisões para preservar o recorte. | `resultado_tcc_contribuicoes_ampliado.xlsx` |
| **03 — Antes/Depois** | Identifica a primeira e a última revisão acadêmica de cada grupo/verbete e confere métricas e estados; mantém a aba complementar de Wikidata. | `resultado_tcc_antes_depois_ampliado.xlsx` |
| **04 — Benchmark** | Documenta o sorteio de artigos bons e destacados e a consulta das métricas, preservando a amostra histórica de 200 artigos. | `resultado_tcc_benchmark_artigos_top.xlsx` |
| **05 — Indicadores** | Aplica as cinco fórmulas, calcula variações, resultados individuais, resultados de grupos e rankings. | `Resultados_Indicadores_TCC_revisado.xlsx` |
| **06 — Gráficos** | Gera cada figura em uma célula independente: comparações, distribuições, rankings, radares, heatmaps e visões dos verbetes criados/preexistentes. | 19 arquivos `.png` |

## Planilhas de entrada: nomes para colocar na pasta `dados/brutos`

Os nomes abaixo correspondem às versões utilizadas na pesquisa. Você pode renomear seus Excel para que coincidam exatamente com esta lista.

| Nome no GitHub (`dados/brutos/`) | Origem / utilização |
|---|---|
| `Wikipedia Alunos.xlsx` | Cadastro acadêmico de estudantes, disciplinas e usuários |
| `Tabela de Verbetes.xlsx` | Grupos, integrantes e verbetes das atividades |
| `Cruzamento_Wikipedia.xlsx` | Cruzamentos e identificação dos verbetes |
| `df_tcc_base_consolidada_corrigida.xlsx` | **Base auditada**: ajustes manuais, mapa de grupos e decisões de correspondência. Necessária para reproduzir as 243 participações |
| `resultado_tcc_contribuicoes_ampliado.xlsx` | **Recorte histórico** das 1.021 revisões e das 177 participações; usado no modo padrão do notebook 02 |
| `resultado_tcc_antes_depois_ampliado.xlsx` | **Recorte histórico** dos 70 estados Antes/Depois e métricas complementares. |
| `resultado_tcc_benchmark_artigos_top.xlsx` | **Benchmark histórico**: 100 artigos bons + 100 destacados |

**Opcional:** `populacoes_benchmark_2026.xlsx`, com duas abas (`Bons`, `Destacados`) e colunas `pageid`, `title`, se você tiver guardado as **listas completas de candidatos** do momento do sorteio. Esse arquivo não consta entre as planilhas finais localizadas e **não foi criado artificialmente**.

### Arquivos produzidos em `dados/processados`

Execute os notebooks na ordem **01 → 02 → 03 → 04 → 05 → 06**. As saídas são:

1. `base_participacoes_consolidada.xlsx`
2. `resultado_tcc_contribuicoes_ampliado.xlsx`
3. `resultado_tcc_antes_depois_ampliado.xlsx`
4. `resultado_tcc_benchmark_artigos_top.xlsx`
5. `Resultados_Indicadores_TCC_revisado.xlsx`
6. Imagens em `resultados/graficos/` (sem Excel adicional)

Em alguns casos, o nome do Excel de saída é igual ao nome da fonte histórica, mas **as pastas são diferentes**. Não sobrescreva os arquivos de `dados/brutos/`.

## Como executar

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

Abra os notebooks na pasta `notebooks/` ou execute a partir da raiz do repositório. Eles identificam automaticamente a pasta raiz e gravam os resultados em `dados/processados/` e `resultados/graficos/`.

**Coletas pela API:** antes de ativar os trechos de consulta, troque `seu-email@exemplo.com` por um contato válido na variável `USER_AGENT`/`AGENTE`. As requisições usam paginação e repetição em erros temporários. Os notebooks não executam automaticamente uma nova coleta, porque os dados da Wikipédia podem mudar.

## Amostragem dos artigos bons e destacados — `random_state=42`

Foram usados **100 artigos bons** e **100 destacados**, sorteados da população elegível de cada classificação, com semente **42**. O procedimento do notebook 04 é:

1. Identificar os artigos elegíveis do domínio principal (namespace 0).
2. Remover repetições por `pageid`.
3. Identificar artigos que aparecem nas duas classificações; os **quatro casos sobrepostos** são mantidos somente como *destacados* e retirados dos *bons*.
4. Sortear 100 artigos de cada grupo com `pandas.DataFrame.sample(n=100, random_state=42)`.
5. Consultar as métricas dos 200 identificadores selecionados.

A semente **não é o número de artigos**: é o estado inicial do gerador pseudoaleatório. Com **a mesma lista de candidatos na mesma ordem**, o sorteio produz os mesmos resultados. Se a lista mudar — por novos artigos, desclassificações, alterações de ordem ou exclusão de sobreposições —, usar `42` não garante a mesma seleção.

Na pesquisa, foram documentadas populações aproximadas de **2.010 artigos bons** e **1.578 destacados** e a consulta às listas em **14/10/2026**. A planilha final preserva a **amostra selecionada**, mas não a listagem integral do momento do sorteio. Por esse motivo, o notebook mantém os **200 `pageid` históricos** por padrão. Um sorteio realmente idêntico depende de recuperar as listas completas daquela coleta e reproduzir sua ordem original; ordenar por `pageid` é uma convenção explícita do notebook, não uma garantia sobre a ordem original.

## Indicadores de qualidade

As cinco dimensões são **completude, verificabilidade, estrutura e organização, conectividade e recursos visuais**. Cada indicador fica entre **0 e 1**. A soma de **0 a 5** é usada apenas como recurso exploratório de comparação e ranking, não como índice validado de qualidade global.

- **Completude:** extensão do artigo em bytes, referência de 25.000.
- **Verificabilidade:** quantidade de referências (60%) e densidade de referências por 10 mil bytes (40%).
- **Estrutura e organização:** seções (90%) e presença de infobox (10%).
- **Conectividade:** links internos (50%), links externos (20%), categorias (20%) e presença de seção “Ver também” (10%).
- **Recursos visuais:** quantidade de imagens (70%) e densidade de imagens por 10 mil bytes (30%).

As referências são contadas pela regra de detecção de `<ref>`, aplicada uniformemente às bases. Isso não é uma avaliação da confiabilidade de cada fonte.

### Diferentes unidades de comparação

- **Verbetes Antes/Depois:** comparação dos estados completos da página; pode incluir intervenções de terceiros entre as revisões acadêmicas.
- **Contribuição individual:** a chave é **aluno + verbete**. Se alguém editou dois verbetes em atividades diferentes, são duas participações independentes. **Não** há ranking agregado por pessoa entre verbetes.
- **Contribuição agregada:** somente soma dos **saldos brutos** dos integrantes de cada grupo/verbete. Depois do agrupamento, saldos negativos recebem zero para calcular os indicadores. Os valores brutos ficam disponíveis nas planilhas.
- **Gráficos de pré-existentes com duas cores:** usam a pontuação inicial e a diferença após **recalcular** os indicadores sobre as métricas iniciais acrescidas dos saldos positivos dos estudantes. Não somam dois indicadores já normalizados. O resultado estimado pode não coincidir com o estado *Depois* observado na Wikipédia.

## Reprodutibilidade e limites

Estes notebooks são uma **reconstrução documentada do procedimento**, elaborada a partir das planilhas finais, dos arquivos de auditoria e das decisões metodológicas da pesquisa. **Não são uma cópia recuperada dos scripts originais** de todas as coletas. Isso é relevante em três situações:

1. A consolidação final dependeu de **ajustes manuais de identificação**, documentados na base auditada.
2. A extração do wikitext depende de **regras de contagem**. O notebook 02 inclui uma implementação explícita de referência; uma recaptura deve ser confrontada com os saldos históricos antes de substituir dados.
3. A composição de uma amostra sorteada exige **lista, ordenação e semente originais**; o benchmark congelado preserva o resultado histórico sem fingir que uma lista atual é a mesma de 2026.

Os dados e as planilhas devem passar por conferência de privacidade antes de serem publicados. Nomes de estudantes, vínculos com disciplinas e identificadores de conta constam das bases; prefira publicar somente o necessário ou versões anonimizadas quando não houver autorização para divulgação.

## Documentação útil

- [API MediaWiki](https://www.mediawiki.org/wiki/API:Main_page)
- [Revisões via API](https://www.mediawiki.org/wiki/API:Revisions)
- [Contribuições de usuários](https://www.mediawiki.org/wiki/API:Usercontribs)
- [Membros de categorias](https://www.mediawiki.org/wiki/API:Categorymembers)


Autores

Este projeto foi desenvolvido como parte do Trabalho de Conclusão de Curso (TCC) intitulado "Medidas de Avaliação da Qualidade de Verbetes da Wikipédia: uma análise a partir de contribuições acadêmicas".

Autores:

- **Carrambert Albuquerque** — [@Carrambert](https://github.com/Carrambert)

- **Gabriely Oliveira da Silva Batista** — [@GabrielyS](https://github.com/GabrielyS)

O repositório reúne os códigos, as bases de dados e os resultados das análises desenvolvidas durante a pesquisa.
