# Grafos sintéticos vs. rede real Facebook ego: relatório (versão 2.0, corrigida)

> **Nota.** Este texto é a versão **corrigida** do relatório. O relatório e o notebook como foram entregues são a **v1.0** (notebook em [`../original/`](../original/), tag `v1.0`); o PDF entregue não está neste repositório. As diferenças estão listadas na seção *Errata* do [README](../README.md). Todos os números abaixo vêm da execução do notebook [`../notebooks/grafos_redes_sociais.ipynb`](../notebooks/grafos_redes_sociais.ipynb).

**Autores:** Arthur Costa Marques, Conrado Marques Martins, Fábio Barbosa Gonsalez e Patrick Rovaron Franco.
**Disciplina/docente:** [PREENCHER]

## 1. Introdução e metodologia

O trabalho tem duas partes. Na **Parte 1**, geramos famílias de grafos sintéticos (Erdős–Rényi, Barabási–Albert e Watts–Strogatz) com sementes fixas e extraímos métricas estruturais. Na **Parte 2**, comparamos a rede real *ego-Facebook* (SNAP; McAuley e Leskovec, NIPS 2012; 4039 nós, 88234 arestas) com paralelos sintéticos de mesmo *n* e grau médio.

**Amostragem, com precisão.** Na Parte 1 todos os grafos têm n ≤ 500, então caminhos mínimos e diâmetro são **exatos**; só a betweenness usa amostragem (k = min(200, n), exata para n = 100). Na Parte 2, o caminho médio e o diâmetro usam uma amostra de 500 fontes e a betweenness usa k = 500. O caminho médio exato do grafo real é 3,6925 contra 3,706 estimado (erro de 0,4%), e o diâmetro exato é 8.

Grade da Parte 1: n ∈ {100, 300, 500}; sementes {0, 1, 2}; ER com p ∈ {0,01; 0,05; 0,1; 0,2}; BA com m ∈ {1, 2, 5, 10}; WS com k ∈ {4, 6, 10} e β ∈ {0; 0,01; 0,1; 0,5; 1}. Total de 207 grafos. As métricas são calculadas no maior componente conexo (LCC).

## 2. Parte 1: visão geral

- O caminho médio L do **ER diminui** com n nesta grade (2,84; 2,79; 2,49), porque p é fixo e o grau médio cresce. O do **BA cresce** (2,98; 3,54; 3,78). O do **WS** com β = 0 cresce linearmente com n (k = 4: 12,9; 37,9; 62,9). A afirmação de que os três modelos crescem como log(n) não se sustenta.
- A variância do grau do BA é maior que a de ER e WS, compatível com cauda pesada.
- A assortatividade do WS com β = 0 (grafo regular) é indefinida (NaN).

## 3. Parte 1: questões de pesquisa

**Q1. ER: o clustering global acompanha p?** Sim para p ≥ 0,05 (C/p entre 0,99 e 1,11; para p = 0,2, C ≈ 0,199 a 0,200). Em p = 0,01 o C medido é maior (0,040; 0,017; 0,012 para n = 100, 300, 500), porque 8 dos 9 grafos com p = 0,01 não são conexos e o clustering é medido num LCC pequeno e atípico: um efeito de seleção, não flutuação amostral. Pearson(p, C) = 0,965 com todos os grafos e 0,998 só com os conexos (apenas 4 valores de p; a razão C/p é mais informativa).

**Q2. BA: redes livres de escala tendem a ser assortativas?** Não: todas as redes BA do grid são dissortativas (média geral −0,119). A magnitude **diminui** com m: −0,191 (m = 1), −0,144 (m = 2), −0,086 (m = 5), −0,057 (m = 10), e também diminui com n (m = 1: −0,249; −0,176; −0,148). *A versão 1.0 afirmava que a dissortatividade aumenta com m; isso estava errado.*

**Q3. WS: o regime small-world depende mais de β ou de k?** Em β = 0,01 e 0,1, para k = 4, 6 e 10, C(β)/C(0) permanece alto (≈ 0,97 e ≈ 0,74) enquanto L(β)/L(0) cai (0,45 a 0,53 e 0,23 a 0,29). A folga C − L é semelhante entre os k (0,44 a 0,53). Em β ≥ 0,5 o clustering colapsa. **Não é possível afirmar** que a janela é independente de k nem localizar suas bordas: a grade só tem dois valores de β nessa região, e a dependência de n é mais forte que a de k (L/L(0) em β = 0,01 vai de ≈ 0,70–0,79 com n = 100 a ≈ 0,26–0,33 com n = 500).

## 4. Parte 2: comparação com o grafo real

| modelo | n | m | ⟨k⟩ | grau máx. | C global | C médio | L | diâmetro | assort. |
|---|---|---|---|---|---|---|---|---|---|
| Facebook | 4039 | 88234 | 43,69 | 1045 | 0,5192 | 0,6055 | 3,706 | 8 | +0,0636 |
| ER | 4039 | 87553 | 43,35 | 67 | 0,0108 | 0,0108 | 2,611 | 3 | +0,0031 |
| BA (m = 22) | 4039 | 88374 | 43,76 | 522 | 0,0366 | 0,0382 | 2,510 | 3 | −0,0111 |
| WS (k = 44, β = 0,1) | 4039 | 88858 | 44,00 | 51 | 0,5325 | 0,5347 | 2,966 | 4 | +0,0042 |

(Paralelos com uma única semente, 42.)

- C médio do real ≈ **56×** o do ER (C global ≈ 48×).
- L do real é **1,42×** o do ER; é curto em termos absolutos, mas não igual ao dos paralelos.
- σ de Humphries e Gurney (2008) = **39,6** (≫ 1).
- Grau máximo 1045; **apenas um** nó (107) tem grau > 1000. O mínimo do real é 1; o do BA é 22.
- Assortatividade do real pequena e positiva (+0,064).

### 4.1. A rede é livre de escala, small-world ou aleatória?

Small-world com cauda pesada; não é aleatória. Evidências: σ ≈ 39,6; C ≈ 56× o do ER; WS reproduz o clustering e o BA reproduz parte da heterogeneidade de grau, mas nenhum modelo captura os dois. Ajuste de lei de potência (biblioteca `powerlaw`, cauda grau ≥ 47, 1250 nós): α ≈ 2,51 ± 0,04. Porém o teste de razão de verossimilhança **rejeita a lei de potência pura** em favor de alternativas com corte (R ≈ −110, p ≈ 0): a cauda tem corte exponencial, o que é compatível com "broad-scale" e **não** com "livre de escala". Ressalva: os maiores graus são de poucos ego-nodes (item 4.2).

### 4.2. Quais os 3 vértices mais importantes?

Pelo ranking médio de grau, betweenness (k = 500), closeness e PageRank: **107, 1684 e 1912**. Os três são **ego-nodes** (verificado nos arquivos `<id>.edges` do pacote original do SNAP; os 10 egos são 0, 107, 348, 414, 686, 698, 1684, 1912, 3437 e 3980). Como um ego tem aresta para todos os seus amigos por construção do dataset, a importância é em boa parte um artefato da coleta. Remover os 3 deixa o LCC em 0,991 (27 componentes); remover os 10 egos deixa 0,924 (91 componentes). A afirmação anterior de que remover um ego "desconecta significativamente a rede" não se sustenta.

### 4.3. Perguntas qualitativas

**(a) Robustez a falhas?** Medimos em passos de 1% até 40% (LCC/n com n original). O ataque por grau é pior que a falha aleatória, mas a queda é **gradual, com degraus**:

| removido | ataque estático | ataque adaptativo | aleatório (média de 30) |
|---|---|---|---|
| 10% | 0,878 | 0,808 | 0,895 |
| 20% | 0,658 | 0,474 | 0,780 |
| 27% | 0,423 | 0,423 | 0,699 |
| 40% | 0,327 | 0,314 | 0,549 |

LCC/n cai abaixo de 50% com **23%** removidos (estático) e **19%** (adaptativo); nas falhas aleatórias, não até 40% na média (a pior das 30 sequências cruza 50% em 34%). Com 10% removidos o LCC ainda é ≈ 0,88: a versão 1.0 dizia que a rede "se fragmenta drasticamente" nessa fração, o que não se sustenta.

**(b) Difusão.** Estruturalmente favorável (caminho curto, hubs, clustering), mas a modularidade alta (Louvain, Q ≈ 0,835, 15 a 16 comunidades; ER equivalente: 0,136) sugere contenção em comunidades. É interpretação da estrutura; **nenhuma difusão foi simulada**.

**(c) Preservação.** Há muita redundância local (1.612.010 triângulos, valor igual ao publicado pelo SNAP). A robustez medida mostra queda gradual. Afirmações sobre pontes inter-comunitárias críticas não foram testadas.

**(d) Navegabilidade.** Discussão teórica, sem experimento: no sentido de Kleinberg (2000), navegabilidade exige que as ligações de longo alcance sigam uma distribuição compatível com uma estrutura subjacente; caminho curto e clustering alto não bastam, e este trabalho não simulou roteamento guloso.

## 5. Conclusão e limitações

Confirmado pelos dados: C ≈ p no ER (com ressalva do LCC para p = 0,01); BA dissortativo, com magnitude decrescente em m e n; no WS, C alto com L baixo para β = 0,01 e 0,1; e o Facebook ego é small-world com cauda pesada e corte, bem acima do ER em clustering. **Não confirmado ou corrigido:** "dissortatividade aumenta com m"; "janela independente de k"; "fragmenta drasticamente em 10%"; vários números (σ, razões de C e L).

**Limitações:** 3 sementes e sem intervalos de confiança na Parte 1; grade pequena; uma única rede real, coletada em 2012, formada por 10 ego-networks; paralelos sintéticos com uma única semente; parte da estrutura de hubs reflete a coleta.

## Referências

- McAuley, J.; Leskovec, J. *Learning to Discover Social Circles in Ego Networks.* NIPS 2012. Dados: SNAP, <https://snap.stanford.edu/data/ego-Facebook.html>.
- Watts, D. J.; Strogatz, S. H. *Collective dynamics of 'small-world' networks.* Nature, 1998.
- Humphries, M. D.; Gurney, K. *Network 'small-world-ness'.* PLoS ONE, 2008.
- Clauset, A.; Shalizi, C. R.; Newman, M. E. J. *Power-law distributions in empirical data.* SIAM Review, 2009.
- Kleinberg, J. *Navigation in a small world.* Nature, 2000.
