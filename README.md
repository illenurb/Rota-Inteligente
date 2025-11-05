#Projeto: Rota Inteligente - Sabor Express
Este projeto implementa uma solução de otimização de rotas para a empresa de delivery "Sabor Express". A solução utiliza um grafo para representar o mapa da cidade, o algoritmo de Dijkstra para encontrar o menor caminho entre os pontos e o K-Means para agrupar entregas próximas, otimizando o tempo e o custo das operações.
# Rota Inteligente: Otimização de Entregas com Algoritmos de IA para Sabor Express

## 1. Descrição do Projeto

Este projeto implementa uma solução de otimização de rotas para a empresa fictícia de delivery de alimentos "Sabor Express". Utilizando conceitos de Inteligência Artificial, o sistema é capaz de agrupar pontos de entrega próximos e calcular o caminho mais eficiente para cada entregador, visando reduzir custos operacionais e o tempo de entrega.

A solução é baseada em:
- **Grafos**: Para representar o mapa da cidade, onde os nós são localidades e as arestas são ruas com pesos (distância ou tempo).
- **Algoritmo de Clustering (K-Means)**: Para agrupar geograficamente as entregas e atribuí-las a diferentes entregadores.
- **Algoritmo de Busca (Dijkstra)**: Para encontrar o caminho mais curto entre dois pontos no grafo.
- **Heurística do Vizinho Mais Próximo**: Para determinar a ordem de visita das entregas dentro de um mesmo cluster.

## 2. Estrutura do Projeto

O projeto está organizado da seguinte forma:

- **/src**: Contém todo o código-fonte da aplicação, dividido em módulos.
- **/data**: Armazena os arquivos de dados, como o mapa da cidade (`mapa_cidade.csv`) e a lista de entregas (`pontos_entrega.csv`).
- **/outputs**: Diretório onde as visualizações gráficas (mapa, clusters, rotas) são salvas.
- **/docs**: Contém a documentação do projeto.
- `requirements.txt`: Lista das dependências Python necessárias.

## 3. Como Executar o Projeto

Siga os passos abaixo para executar a solução em sua máquina local.

### Pré-requisitos

- Python 3.8 ou superior instalado.
- `pip` (gerenciador de pacotes do Python).

### Passo a Passo

**1. Clone o Repositório**
(Se o projeto estivesse no Git)
```bash
git clone <URL_DO_REPOSITORIO>
cd Sabor_Express_IA
Como alternativa, crie a estrutura de diretórios e copie os arquivos fornecidos.
2. Crie um Ambiente Virtual (Recomendado)
Um ambiente virtual isola as dependências do seu projeto.
code
Bash
python -m venv venv
source venv/bin/activate  # No Windows: venv\Scripts\activate
3. Instale as Bibliotecas Necessárias
Instale todas as dependências listadas no arquivo requirements.txt.
code
Bash
pip install -r requirements.txt
4. Execute o Script Principal
O script main.py irá carregar os dados, processar a otimização e gerar os outputs.
code
Bash
python src/main.py
5. Verifique os Resultados
Após a execução, a saída do terminal mostrará os clusters e as rotas otimizadas. Além disso, os seguintes arquivos de imagem serão gerados no diretório /outputs:
mapa_cidade.png: Visualização do grafo que representa a cidade.
clusters_de_entrega.png: Mostra os agrupamentos de entregas.
rotas_otimizadas.png: Exibe o trajeto otimizado para cada entregador no mapa.
4. Personalização
Você pode personalizar a simulação alterando:
data/mapa_cidade.csv: Modifique ou adicione ruas e conexões para representar um mapa diferente.
data/pontos_entrega.csv: Altere a lista de entregas a serem processadas.
src/main.py: Ajuste os parâmetros PONTO_PARTIDA e NUMERO_ENTREGADORES para diferentes cenários.
code
Code
---

### **4. Outputs Relevantes (`/outputs`)**

Após a execução, a pasta `/outputs` conterá as seguintes imagens:

**`clusters_de_entrega.png`**

*Gráfico visual que mostra como os pontos de entrega foram divididos entre os entregadores.*

**`rotas_otimizadas.png`**

*Mapa final exibindo os caminhos otimizados que cada entregador deve seguir para completar suas entregas e retornar à base.*

Esta solução completa fornece não apenas o código funcional, mas também a estrutura de projeto, os dados de exemplo e a documentação necessária para entender e executar o sistema, atendendo a todos os requisitos do desafio proposto.
