# Análise de redes complexas: Crawling da wikipédia e centralidades

Este projeto visa a construção, análise e visualização de uma rede complexa (grafo direcionado) a partir de páginas da Wikipédia em português. Utilizamos o algoritmo de **Busca em Largura (BFS)** para o *crawling* e a biblioteca **NetworkX** para processamento e normalização dos dados. A visualização e a análise detalhada das métricas de centralidade e coesão foram realizadas utilizando o **Gephi**.

#### Autores:
- Diego Rodrigo Rabelo dos Santos
- Leonardo Pessoa Cavalcanti
- Lucas Marques dos Santos

#### Link para o vídeo (Youtube:
https://www.youtube.com/watch?v=sLV3YGu8Bpc
  
---

##  Metodologia de construção da rede

### 1. Estratégia de coleta (Crawling)

* **Seeds (Páginas Semente):** A coleta se iniciou a partir de 5 páginas semente com foco em nicho regional (Natal/RN), escolhidas para criar um grafo mesclado (fusão de dados): `["Cajueiro", "Potiguar", "Grafith", "Complexo Cultural Rampa", "Ginga com Tapioca"]`.
* **Profundidade:** O *crawling* foi limitado a uma altura de **Nível 2** (`layer < 3`), garantindo uma exploração controlada.
* **Linguagem:** O foco foi em links da Wikipédia em português.

### 2. Heurística e estrutura de dados

Para gerenciar a alta demanda computacional da exploração até o Nível 2 (crescimento exponencial), foi implementada uma heurística e utilizada a estrutura de dados mais eficiente, formalizando a abordagem da **Busca em Largura (BFS)**:

* **Heurística (Amostragem de Links):** Foi estabelecido um limite de **50 links de saída** (`link_limit = 50`) por página para inclusão na fila de processamento. Essa amostragem reduziu drasticamente a taxa de ramificação da busca, tornando o *crawling* do Nível 2 viável.
* **Estrutura de Dados:**
    * **Fila (`todo_lst`):** Utilizada para gerenciar a ordem de exploração, garantindo o processamento em camadas (BFS).
    * **Conjunto (`todo_set` e `done_set`):** Utilizados para armazenar nós já processados, permitindo verificações de pertencimento em tempo $O(1)$, prevenindo *loops* e redundância.

### 3. Processamento e normalização

O grafo (NetworkX `DiGraph`) passou por etapas de limpeza essenciais antes da análise:
* Remoção de *self-loops*.
* Contração de nós duplicados por pluralização e hifenização, utilizando `nx.contracted_nodes`.
* O grafo final foi exportado para o Gephi no formato **`cna.graphml`**.

---

## Resultados e análise de estrutura (Gephi)

A rede finalizada para análise no Gephi possui **16.412 nós** e **117.748 arestas** (após a normalização).

### 1. Centralidade e Influência

Foram geradas quatro figuras (utilizando o layout **Force Atlas 2** com alto *scaling*) que destacam a hierarquia de influência na rede. O **tamanho** dos vértices foi definido pelo **Grau de Entrada (`In-Degree`)**.

| Métrica | Propriedade Visual | Análise do Resultado |
| :--- | :--- | :--- |
| **Grau** | Tamanho $\propto$ In-Degree | Destacou as páginas mais referenciadas e populares, como grandes hubs (Ex: o nó vermelho e grande no centro das figuras). |
| **Closeness** | Cor $\propto$ Closeness Centrality | A cor destacou os nós mais eficientes e centrais na disseminação de informações. |
| **Betweenness** | Cor $\propto$ Betweenness Centrality | A cor destacou as **pontes** (nós mediadores), que são cruciais para o fluxo de informação entre diferentes regiões da rede. |
| **Eigenvector** | Cor $\propto$ PageRank | A cor destacou os nós de **"autoridade"**, que são referenciados por outros nós já influentes. |

### 2. Coesão e núcleo da rede

Foi gerada uma figura (utilizando o filtro K-Core no Gephi) para analisar a coesão estrutural, com o tamanho dos vértices proporcional ao *Degree*.

* **K-Core Máximo:** Foi identificado o valor máximo de **$K_{Core} = 71$**.
* **K-Shell:** O **K-Shell (camada envolvente)** foi definido em $K_{Shell} = 68$ (ou valor adjacente), representando a primeira camada que se liga ao núcleo.
* **Visualização:** A figura revela que o K-Core 71 está dividido em **dois Componentes Gigantes** (mostrados em vermelho e azul nas extremidades da figura), o que sugere a existência de dois domínios de informação igualmente robustos, mas sem links diretos fortes entre si.

### 3. Análise de comunidades

A rede foi analisada sob a perspectiva de comunidades, utilizando o layout **Force Atlas 2** para melhor separação:

* **Critério de comunidade:** A **Cor** dos vértices foi definida pela **Classe de Modularidade**, separando os clusters de páginas que têm alta densidade de links internos (comunidades temáticas). Foram encontrados 19 grupos, cada um identificado por uma cor no gráfico.
* **Tamanho do vértice:** O **Tamanho** dos vértices foi definido pelo **Grau de Entrada (`In-Degree`)**, destacando a popularidade dos nós dentro de cada comunidade. Os resultados demonstram como os 5 tópicos iniciais se fundiram em comunidades maiores dentro da rede.

---

## Detalhes para reprodução do projeto

Para replicar o processo de *crawling*, análise e visualização da rede, siga os passos abaixo.

### Pré-requisitos

O projeto depende das seguintes bibliotecas Python e software de análise:

* Python 3.x
* Jupyter Notebook / Google Colab
* Bibliotecas Python: NetworkX e API da Wikipedia (`pip install networkx wikipedia`)
* Gephi (para análise e visualização)

### Passos

1. **Execução do código python:** Executar o script de *crawling* para gerar o arquivo **`cna.graphml`**.

* Verifique as SEEDs: ["Cajueiro", "Potiguar", "Grafith", "Complexo Cultural Rampa", "Ginga com Tapioca"].

* Ajuste da heurística (Opcional): A variável link_limit (heurística de amostragem) está definida como 50. Se o tempo de execução for muito longo, você pode diminuí-la.

* Execute o código. O tempo total de crawling (Nível 2) e limpeza é de aproximadamente 38 minutos.
   
2. **Importação no Gephi:** Abrir o Gephi e importar o arquivo **`cna.graphml`**.
3. **Análise e visualização:** No Gephi, aplicar as métricas (Grau, Modularidade, PageRank, Closeness, Betweenness) e filtros (em particular o filtro K-Core e a coloração por Modularidade) conforme descrito nos resultados.
