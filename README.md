# Grafos sintéticos vs. rede real Facebook ego

Análise comparativa entre grafos sintéticos (Erdős–Rényi, Barabási–Albert e Watts–Strogatz) e a rede real *ego-Facebook* do SNAP (4039 nós, 88234 arestas). Projeto da disciplina de Grafos e Redes Sociais (PUC-Campinas), entregue em jun/2026 por um grupo de quatro pessoas e revisado depois da entrega.

- **Versão 2.0 (esta, corrigida):** `notebooks/` e `docs/relatorio.md`.
- **Versão 1.0 (como entregue):** `original/` e a tag `v1.0`. Veja a seção [Errata](#errata).

**Autores:** Arthur Costa Marques, Conrado Marques Martins, Fábio Barbosa Gonsalez e Patrick Rovaron Franco.
**Disciplina/docente:** Grafos e Redes Sociais, Fernando S. de Aguiar Neto e Leonardo Rezende

## Perguntas de pesquisa

Parte 1 (grafos sintéticos, 207 grafos, 3 sementes):

1. **Q1.** Em G(n, p), o clustering global acompanha p?
2. **Q2.** Redes Barabási–Albert são assortativas ou dissortativas, e como isso varia com m?
3. **Q3.** No Watts–Strogatz, o regime small-world depende mais de β ou de k?

Parte 2 (rede real): a rede é aleatória, small-world ou livre de escala? Quão robusta é a falhas e ataques? Quais são os vértices mais importantes? O que se pode dizer, de forma qualitativa, sobre difusão, preservação de informação e navegabilidade?

## Como rodar

Requer Python 3 (testado com 3.14) e acesso à internet (o dataset é baixado do SNAP na primeira execução; ele **não** está neste repositório).

```bash
python -m venv .venv
source .venv/bin/activate        # no Windows: .venv\Scripts\activate
pip install -r requirements.txt
cd notebooks
jupyter nbconvert --to notebook --execute --inplace grafos_redes_sociais.ipynb
```

A execução leva alguns minutos (o grafo real e as centralidades são a parte mais lenta). Os CSVs e as figuras são gravados em `results/`; o dataset é baixado em `data/` (ignorado pelo git). As sementes são fixas, então os resultados são reprodutíveis com as versões do `requirements.txt`.

## Estrutura

```
notebooks/   notebook corrigido (v2.0), com as saídas embutidas
original/    notebook como entregue (v1.0), sem alterações (não renderiza no GitHub por causa dos metadados do Colab; baixe o arquivo para abrir)
results/     CSVs e figuras gerados pelo notebook
docs/        relatório corrigido (relatorio.md)
```

## Principais resultados (execução corrigida)

| Pergunta | Resultado |
|---|---|
| Q1 (ER) | C ≈ p para p ≥ 0,05 (C/p entre 0,99 e 1,11). Para p = 0,01 o C medido é maior, por efeito de seleção do maior componente (8 dos 9 grafos não são conexos). |
| Q2 (BA) | Sempre dissortativo (média −0,119), com magnitude **decrescente** em m (−0,191; −0,144; −0,086; −0,057 para m = 1, 2, 5, 10) e em n. |
| Q3 (WS) | Em β = 0,01 e 0,1, C(β)/C(0) fica alto (≈ 0,97 e 0,74) e L(β)/L(0) cai (≈ 0,45–0,53 e 0,23–0,29) para k = 4, 6 e 10. Não dá para afirmar independência de k nem localizar as bordas da "janela" (só dois β na região; n pesa mais que k). |
| Real vs. ER | C médio ≈ 56× o do ER; L do real é 1,42× o do ER (3,71 vs. 2,61); σ de small-world ≈ 39,6. |
| Distribuição de grau | Cauda pesada; ajuste de lei de potência na cauda dá α ≈ 2,51, mas a lei de potência pura é rejeitada frente a alternativas com corte (R ≈ −110): "broad-scale", não "livre de escala". |
| Estrutura | 1.612.010 triângulos; modularidade (Louvain) ≈ 0,835 com 15 a 16 comunidades (ER equivalente: 0,136). |
| Robustez | O ataque por grau é pior que falhas aleatórias, mas a queda é gradual: LCC/n < 50% em 23% (estático) e 19% (adaptativo) dos nós removidos; aleatório não cruza 50% até 40% (em média). Com 10% removidos o LCC é ≈ 0,88. |
| Vértices top | 107, 1684 e 1912, **os três são ego-nodes** do dataset: parte da "importância" é artefato da coleta. |

Detalhes, tabelas e gráficos: [`notebooks/grafos_redes_sociais.ipynb`](notebooks/grafos_redes_sociais.ipynb) e [`docs/relatorio.md`](docs/relatorio.md).

## Limitações

- **3 sementes** por configuração e **sem intervalos de confiança** na Parte 1; grade pequena (n ≤ 500; apenas 4 valores positivos de β).
- **Uma única rede real**, com **dados de 2012**, formada por 10 ego-networks; não se generaliza para "redes sociais" nem para o Facebook atual.
- Paralelos sintéticos da Parte 2 com uma única semente; caminho médio, diâmetro e betweenness do grafo real estimados por amostra de fontes (o caminho médio exato foi checado: 3,6925 vs. 3,706 estimado).
- Difusão, preservação de informação e navegabilidade são discutidas só de forma qualitativa ou teórica: nenhum processo foi simulado.
- O ajuste de lei de potência é só da cauda, com `xmin` estimado, e não prova mecanismo gerador.

## Errata

Mudanças da v1.0 (como entregue) para a v2.0 e o motivo de cada uma. A v1.0 continua disponível em `original/` e na tag `v1.0`.

**Correção de código**

| # | v1.0 | v2.0 | Por quê |
|---|---|---|---|
| 1 | `extract_metrics` devolvia `n` e `m`, que sobrescreviam o `n` nominal (9 dos 36 ER) e o parâmetro `m` do BA (36 de 36). | Chaves renomeadas para `n_lcc` e `m_arestas`; `n` e `m` nominais preservados. | Bug de sobrescrita no `run_one`. O eixo do gráfico Q2 mostrava número de arestas (99, 196, 299, ...) em vez de m ∈ {1, 2, 5, 10}; "caminho médio vs n" plotava o ER com 15 valores de n; cores da Q1 usavam o LCC. (O WS e o agrupamento (n, k) da Q3 não eram afetados.) |
| 2 | Ramo de amostragem de caminhos no `extract_metrics`. | Removido. | Código morto na grade (n ≤ 500, caminhos exatos). |
| 3 | Assortatividade do WS com β = 0 saía `NaN` com `RuntimeWarning`. | `NaN` explícito, documentado. | Grafo regular (variância do grau zero). |
| 4 | Metadados de widgets no formato Colab; saída desatualizada na célula 1. | Sem `metadata.widgets`; kernel fixo; saídas regeneradas. | O GitHub costuma não renderizar o formato Colab. |

**Correção de conclusões**

| # | v1.0 dizia | v2.0 diz | Por quê |
|---|---|---|---|
| 5 | "A dissortatividade aumenta com m." | Diminui com m (e com n). | Recalculado com m nominal. |
| 6 | Q1: desvios em n pequeno são "flutuação amostral". | Efeito de seleção do LCC em p = 0,01. | 8 dos 9 grafos não conexos; C medido no LCC. |
| 7 | Q3: janela β ∈ [0,01; 0,1] "independente de k". | Não sustentável com os dados; curvas por k, dependência de n maior que de k. | A curva da v1.0 era a média sobre k e n; só dois β na região. |
| 8 | Remover ~10% dos hubs "fragmenta drasticamente". | Queda gradual: LCC ≈ 0,88 em 10%; < 50% em 23% (estático) e 19% (adaptativo). | Medição fina (passos de 1%). |
| 9 | "Os três modelos crescem como log(n)." | ER decresce, BA cresce, WS (β = 0) é linear. | Recalculado com n nominal. |
| 10 | Remover um ego "desconecta significativamente a rede". | Remover os 3 maiores deixa o LCC em 0,991. | Medido. |
| 11 | Top 3 são egos "com altíssima probabilidade". | Verificado: 107, 1684 e 1912 são ego-nodes; importância em parte artefato da coleta. | Conferido no pacote original do SNAP. |

**Números corrigidos**

| Grandeza | v1.0 (relatório) | Saída do notebook |
|---|---|---|
| σ small-world | "tipicamente > 50" | 39,62 |
| C do real vs. ER | "centenas de vezes" | 56× (C médio), 48× (C global) |
| L/L(0) em β = 0,1 | "~30%" | 25% |
| L do real vs. ER | "≈" | 1,42× |
| Hubs com > 1000 conexões | "hubs" (plural) | um único nó (107, grau 1045) |

**Afirmações antes sem cálculo, agora calculadas ou reescritas**

- Modularidade (Louvain, sementes fixas), 1.612.010 triângulos e ajuste de lei de potência: calculados no notebook, com as limitações citadas.
- "γ na faixa 2–3": α ≈ 2,51, mas a lei de potência pura é rejeitada frente a alternativas com corte.
- Difusão e preservação: discussão qualitativa, sem número novo. Navegabilidade (Kleinberg): discussão teórica, sem experimento.

**Mudanças de método:** metodologia descrita com precisão (caminhos exatos na Parte 1, só a betweenness amostrada); robustez com passos de 1% até 40%, ataque adaptativo e 30 sequências aleatórias.

## Créditos e dados

- **Dados:** SNAP, *ego-Facebook* (<https://snap.stanford.edu/data/ego-Facebook.html>), arquivo `facebook_combined.txt.gz`, baixado em tempo de execução e não redistribuído aqui.
- **Referência do dataset:** J. McAuley e J. Leskovec. *Learning to Discover Social Circles in Ego Networks.* NIPS 2012.
- Bibliotecas: NetworkX, NumPy, pandas, SciPy, Matplotlib, seaborn, `powerlaw`.

## Licença

[MIT](LICENSE), copyright 2026 dos quatro autores. A licença cobre o código e o texto deste repositório; os dados do SNAP seguem os termos do próprio SNAP.
