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



#Projeto: Rota Inteligente - Sabor Express
#Este projeto implementa uma solução de otimização de rotas para a empresa de delivery "Sabor Express". A solução utiliza um grafo para representar o mapa da cidade, o algoritmo de Dijkstra para encontrar o menor caminho entre os pontos e o K-Means para agrupar entregas próximas, otimizando o tempo e o custo das operações.
#Estrutura de Diretórios

/Sabor_Express_IA
|
|-- /src
|   |-- main.py                # Script principal que executa a solução
|   |-- grafo.py               # Módulo para criação e manipulação do grafo
|   |-- otimizador_rotas.py    # Módulo com os algoritmos de otimização (Dijkstra e K-Means)
|   |-- visualizador.py        # Módulo para gerar visualizações gráficas
|
|-- /data
|   |-- mapa_cidade.csv        # Arquivo com as arestas e pesos do grafo (ruas)
|   |-- pontos_entrega.csv     # Arquivo com os locais de entrega do dia
|
|-- /outputs
|   |-- rotas_otimizadas.png   # Imagem gerada com o mapa de rotas
|   |-- clusters_de_entrega.png# Imagem com os agrupamentos de entrega
|
|-- /docs
|   |-- README.md              # Documentação do projeto
|
|-- requirements.txt           # Lista de bibliotecas necessárias
1. Arquivos de Dados (/data)
Para simular o ambiente, criamos dois arquivos CSV: um para o mapa e outro para os pontos de entrega.
data/mapa_cidade.csv
Este arquivo representa as ruas da cidade. Cada linha é uma conexão (aresta) entre dois pontos (bairros/esquinas) com um peso (distância ou tempo).
code
Csv
origem,destino,peso
A,B,4
A,C,8
B,C,3
B,D,9
C,D,2
C,E,5
D,E,1
D,F,6
E,F,3
F,G,2
G,H,5
H,I,4
H,A,7
data/pontos_entrega.csv
Este arquivo contém a lista de entregas a serem realizadas, com coordenadas x e y para permitir o agrupamento. O ponto de partida é o restaurante, localizado no Ponto 'A'.
code
Csv
pedido_id,ponto_entrega,coord_x,coord_y
1,C,2,8
2,D,3,4
3,F,7,3
4,I,10,9
5,H,9,7
6,E,5,2
2. Código-Fonte Completo e Organizado (/src)
A solução é modularizada para facilitar a manutenção e o entendimento.
src/grafo.py
Este módulo contém a classe Grafo que carrega o mapa da cidade a partir do CSV e o representa em uma estrutura de dados adequada.
code
Python
import pandas as pd
import networkx as nx

class Grafo:
    """
    Classe para representar a cidade como um grafo.
    Carrega os dados de um arquivo CSV e constrói um grafo
    utilizando a biblioteca NetworkX.
    """
    def __init__(self, arquivo_mapa):
        """
        Construtor da classe Grafo.

        Args:
            arquivo_mapa (str): Caminho para o arquivo CSV com os dados do mapa.
        """
        self.arquivo_mapa = arquivo_mapa
        self.grafo = nx.Graph()
        self._carregar_mapa()

    def _carregar_mapa(self):
        """
        Carrega as conexões (arestas) do arquivo CSV e as adiciona ao grafo.
        """
        try:
            df_mapa = pd.read_csv(self.arquivo_mapa)
            for _, row in df_mapa.iterrows():
                self.grafo.add_edge(row['origem'], row['destino'], weight=row['peso'])
        except FileNotFoundError:
            print(f"Erro: O arquivo {self.arquivo_mapa} não foi encontrado.")
            exit()

    def get_grafo(self):
        """
        Retorna o objeto do grafo.
        """
        return self.grafo

    def get_nos(self):
        """
        Retorna uma lista de todos os nós (pontos) no grafo.
        """
        return list(self.grafo.nodes())
src/otimizador_rotas.py
Este módulo contém a lógica principal para otimização, incluindo o algoritmo de Dijkstra e o K-Means para clustering.
code
Python
import pandas as pd
import networkx as nx
from sklearn.cluster import KMeans

class Otimizador:
    """
    Classe responsável por agrupar entregas e encontrar as melhores rotas.
    """
    def __init__(self, grafo):
        """
        Construtor da classe Otimizador.

        Args:
            grafo (networkx.Graph): O grafo que representa o mapa da cidade.
        """
        self.grafo = grafo

    def agrupar_entregas(self, arquivo_entregas, n_clusters=2):
        """
        Agrupa os pontos de entrega em clusters usando o algoritmo K-Means.

        Args:
            arquivo_entregas (str): Caminho para o CSV com os pontos de entrega.
            n_clusters (int): Número de clusters (entregadores).

        Returns:
            dict: Um dicionário onde as chaves são os IDs dos clusters e os
                  valores são listas de pontos de entrega pertencentes a esse cluster.
        """
        df_entregas = pd.read_csv(arquivo_entregas)
        coordenadas = df_entregas[['coord_x', 'coord_y']].values
        
        kmeans = KMeans(n_clusters=n_clusters, random_state=42, n_init=10)
        df_entregas['cluster'] = kmeans.fit_predict(coordenadas)
        
        clusters = {}
        for cluster_id in df_entregas['cluster'].unique():
            pontos_cluster = df_entregas[df_entregas['cluster'] == cluster_id]['ponto_entrega'].tolist()
            clusters[cluster_id] = pontos_cluster
            
        print("Entregas agrupadas em clusters:", clusters)
        return clusters, df_entregas

    def encontrar_menor_caminho(self, origem, destino):
        """
        Encontra o menor caminho entre dois pontos usando o algoritmo de Dijkstra.

        Args:
            origem (str): Nó de origem.
            destino (str): Nó de destino.

        Returns:
            tuple: Uma tupla contendo o custo total e a lista de nós no caminho.
        """
        try:
            custo = nx.dijkstra_path_length(self.grafo, origem, destino, weight='weight')
            caminho = nx.dijkstra_path(self.grafo, origem, destino, weight='weight')
            return custo, caminho
        except nx.NetworkXNoPath:
            return float('inf'), []

    def otimizar_rota_cluster(self, ponto_partida, pontos_cluster):
        """
        Calcula a rota otimizada para um cluster de entregas, encontrando a
        ordem que minimiza a distância total (Problema do Caixeiro Viajante - heurística simples).

        Args:
            ponto_partida (str): O ponto inicial (restaurante).
            pontos_cluster (list): Lista de pontos a serem visitados.

        Returns:
            tuple: Rota final otimizada (lista de nós) e o custo total.
        """
        pontos_nao_visitados = list(pontos_cluster)
        ponto_atual = ponto_partida
        rota_final = [ponto_partida]
        custo_total = 0

        while pontos_nao_visitados:
            menor_custo = float('inf')
            proximo_ponto = None
            
            for ponto in pontos_nao_visitados:
                custo, _ = self.encontrar_menor_caminho(ponto_atual, ponto)
                if custo < menor_custo:
                    menor_custo = custo
                    proximo_ponto = ponto
            
            if proximo_ponto:
                custo_segmento, caminho_segmento = self.encontrar_menor_caminho(ponto_atual, proximo_ponto)
                custo_total += custo_segmento
                # Adiciona o caminho do segmento, exceto o ponto de partida que já está na rota
                rota_final.extend(caminho_segmento[1:])
                
                ponto_atual = proximo_ponto
                pontos_nao_visitados.remove(proximo_ponto)
            else:
                # Não há mais pontos alcançáveis
                break
        
        # Voltar ao ponto de partida
        custo_retorno, caminho_retorno = self.encontrar_menor_caminho(ponto_atual, ponto_partida)
        custo_total += custo_retorno
        rota_final.extend(caminho_retorno[1:])

        return rota_final, custo_total
src/visualizador.py
Este módulo utiliza matplotlib e networkx para desenhar o grafo e as rotas.
code
Python
import matplotlib.pyplot as plt
import networkx as nx
import pandas as pd

class Visualizador:
    """
    Classe para criar visualizações do grafo, clusters e rotas.
    """
    def __init__(self, grafo):
        self.grafo = grafo
        # Posições fixas para um layout consistente
        self.posicoes = nx.spring_layout(self.grafo, seed=42)

    def desenhar_grafo(self, arquivo_saida="outputs/mapa_cidade.png"):
        """
        Desenha o grafo base da cidade e salva em um arquivo.
        """
        plt.figure(figsize=(12, 8))
        nx.draw(self.grafo, self.posicoes, with_labels=True, node_color='lightblue', node_size=700, font_size=10, font_weight='bold')
        labels = nx.get_edge_attributes(self.grafo, 'weight')
        nx.draw_networkx_edge_labels(self.grafo, self.posicoes, edge_labels=labels)
        plt.title("Mapa da Cidade - Sabor Express")
        plt.savefig(arquivo_saida)
        plt.close()

    def desenhar_clusters(self, df_entregas, arquivo_saida="outputs/clusters_de_entrega.png"):
        """
        Desenha o grafo e destaca os clusters de entrega.
        """
        plt.figure(figsize=(12, 8))
        cores_clusters = ['red', 'green', 'blue', 'purple']
        
        # Desenha o grafo base
        nx.draw(self.grafo, self.posicoes, with_labels=True, node_color='lightgray', node_size=500)
        
        # Destaca os nós de entrega
        for cluster_id in df_entregas['cluster'].unique():
            pontos = df_entregas[df_entregas['cluster'] == cluster_id]['ponto_entrega'].tolist()
            nx.draw_networkx_nodes(self.grafo, self.posicoes, nodelist=pontos,
                                   node_color=cores_clusters[cluster_id % len(cores_clusters)],
                                   node_size=700, label=f'Cluster {cluster_id}')
        
        # Destaca o restaurante
        nx.draw_networkx_nodes(self.grafo, self.posicoes, nodelist=['A'], node_color='gold', node_size=1000, node_shape='s')
        
        plt.title("Clusters de Entrega")
        plt.legend()
        plt.savefig(arquivo_saida)
        plt.close()

    def desenhar_rotas(self, rotas_otimizadas, df_entregas, ponto_partida, arquivo_saida="outputs/rotas_otimizadas.png"):
        """
        Desenha as rotas otimizadas para cada cluster.
        """
        plt.figure(figsize=(15, 10))
        cores_rotas = ['red', 'green', 'blue', 'purple']

        # Desenhar o grafo base
        nx.draw(self.grafo, self.posicoes, with_labels=True, node_color='lightblue', node_size=700)
        
        # Desenhar cada rota otimizada
        for i, (cluster_id, rota_info) in enumerate(rotas_otimizadas.items()):
            rota = rota_info['rota']
            cor = cores_rotas[i % len(cores_rotas)]
            
            # Desenha as arestas da rota
            path_edges = list(zip(rota, rota[1:]))
            nx.draw_networkx_edges(self.grafo, self.posicoes, edgelist=path_edges, width=2.5, alpha=0.8, edge_color=cor)
            
            # Destaca os nós de entrega do cluster
            pontos_cluster = df_entregas[df_entregas['cluster'] == cluster_id]['ponto_entrega'].tolist()
            nx.draw_networkx_nodes(self.grafo, self.posicoes, nodelist=pontos_cluster, node_color=cor, node_size=700)

        # Destaca o ponto de partida
        nx.draw_networkx_nodes(self.grafo, self.posicoes, nodelist=[ponto_partida], node_color='gold', node_size=1000, node_shape='s')
        
        plt.title("Rotas de Entrega Otimizadas")
        plt.savefig(arquivo_saida)
        plt.show()
src/main.py
Este é o script orquestrador que une todas as partes da solução.
code
Python
from grafo import Grafo
from otimizador_rotas import Otimizador
from visualizador import Visualizador

def main():
    """
    Função principal para executar a otimização de rotas.
    """
    # --- Parâmetros de Configuração ---
    PONTO_PARTIDA = 'A'  # Ponto de partida (Restaurante)
    NUMERO_ENTREGADORES = 2
    ARQUIVO_MAPA = 'data/mapa_cidade.csv'
    ARQUIVO_ENTREGAS = 'data/pontos_entrega.csv'

    print("--- Rota Inteligente: Sabor Express ---")

    # 1. Carregar o mapa da cidade como um grafo
    print("\n[PASSO 1] Carregando o mapa da cidade...")
    g = Grafo(ARQUIVO_MAPA)
    mapa = g.get_grafo()
    print("Mapa carregado com sucesso!")

    # 2. Inicializar o otimizador e o visualizador
    otimizador = Otimizador(mapa)
    visualizador = Visualizador(mapa)
    
    # Gerar e salvar uma imagem do mapa
    visualizador.desenhar_grafo()
    print("Imagem do mapa salva em 'outputs/mapa_cidade.png'")


    # 3. Agrupar entregas para cada entregador
    print(f"\n[PASSO 2] Agrupando entregas em {NUMERO_ENTREGADORES} clusters (entregadores)...")
    clusters, df_entregas = otimizador.agrupar_entregas(ARQUIVO_ENTREGAS, n_clusters=NUMERO_ENTREGADORES)
    
    # Gerar e salvar visualização dos clusters
    visualizador.desenhar_clusters(df_entregas)
    print("Imagem dos clusters salva em 'outputs/clusters_de_entrega.png'")


    # 4. Calcular a rota otimizada para cada cluster
    print("\n[PASSO 3] Calculando a melhor rota para cada entregador...")
    rotas_otimizadas = {}
    for cluster_id, pontos in clusters.items():
        print(f"\n--- Otimizando Rota para o Entregador {cluster_id + 1} (Cluster {cluster_id}) ---")
        print(f"Pontos de entrega: {pontos}")
        
        rota, custo = otimizador.otimizar_rota_cluster(PONTO_PARTIDA, pontos)
        rotas_otimizadas[cluster_id] = {'rota': rota, 'custo': custo}
        
        print(f"Rota Otimizada: {' -> '.join(rota)}")
        print(f"Custo Total (distância/tempo): {custo:.2f}")

    # 5. Visualizar as rotas finais no mapa
    print("\n[PASSO 4] Gerando visualização das rotas otimizadas...")
    visualizador.desenhar_rotas(rotas_otimizadas, df_entregas, PONTO_PARTIDA)
    print("Visualização das rotas salva em 'outputs/rotas_otimizadas.png'")
    print("\n--- Processo Concluído ---")


if __name__ == "__main__":
    main()
3. Documentação e Instruções de Execução (/docs e requirements.txt)
requirements.txt
Liste todas as bibliotecas Python necessárias para o projeto.
code
Code
pandas
scikit-learn
networkx
matplotlib
#grafos gerados pelo programa
<img width="1200" height="800" alt="image" src="https://github.com/user-attachments/assets/69f54a2e-e2ea-4809-8b9a-bf60d041a6e7" />
<img width="1200" height="800" alt="image" src="https://github.com/user-attachments/assets/3e6db306-d3c1-4a22-b5f6-4e24f0cfa425" />
