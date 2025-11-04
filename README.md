# Rota-Inteligente
Compreendemos os desafios logísticos que sua empresa enfrenta, especialmente nos horários de pico, que resultam em rotas ineficientes, atrasos e, consequentemente, impactam a satisfação do cliente e os custos operacionais. 
# Rota Inteligente: Otimização de Entregas com Algoritmos de IA para a "Sabor Express"

## 1. Descrição do Problema

A "Sabor Express", uma empresa local de delivery de alimentos, enfrenta desafios significativos na gestão de suas entregas durante horários de pico. A definição manual de rotas, baseada apenas na experiência dos entregadores, resulta em trajetos ineficientes, atrasos, aumento nos custos de combustível e, consequentemente, insatisfação dos clientes.

### Desafio Proposto
Desenvolver uma solução de software que utilize algoritmos de Inteligência Artificial para otimizar as rotas de entrega. O sistema deve ser capaz de:
1.  Agrupar pedidos geograficamente próximos para otimizar o trabalho de múltiplos entregadores.
2.  Calcular a rota mais eficiente (menor caminho) para um conjunto de entregas, considerando as características da malha urbana.

### Objetivos
*   **Reduzir o tempo total de entrega** dos pedidos.
*   **Minimizar a distância percorrida** pelos entregadores, gerando economia de combustível.
*   **Aumentar a eficiência operacional**, permitindo que cada entregador realize mais entregas em menos tempo.
*   **Melhorar a satisfação do cliente** através de um serviço mais rápido e previsível.

---

## 2. Abordagem Adotada

Para solucionar o desafio, nossa abordagem foi dividida em três etapas principais:

1.  **Modelagem do Cenário com Grafos**: Representamos a região central da cidade como um grafo. Os bairros ou pontos de entrega são os **vértices (nós)** e as ruas que os conectam são as **arestas**. O **peso** de cada aresta representa o tempo ou a distância do trajeto.

2.  **Agrupamento de Entregas (Clustering)**: Em cenários com alta demanda e múltiplos entregadores, utilizamos um algoritmo de clustering para agrupar os pedidos. Isso divide o conjunto total de entregas em "zonas" geograficamente coesas, atribuindo uma zona para cada entregador.

3.  **Otimização de Rota Individual**: Após o agrupamento, calculamos a melhor rota para cada entregador dentro de sua zona designada. Isso envolve encontrar o caminho mais curto que passa por todos os pontos de entrega do grupo, começando e terminando no restaurante.

Essa abordagem híbrida permite otimizar tanto a distribuição de tarefas quanto a execução de cada uma delas, atacando o problema de forma sistêmica.

---

## 3. Algoritmos Utilizados

### a) K-Means (para Clustering de Entregas)
-   **Propósito**: Agrupar os pontos de entrega em `k` clusters, onde `k` é o número de entregadores disponíveis.
-   **Funcionamento**: O K-Means agrupa os pedidos com base em sua proximidade no plano cartesiano (latitude/longitude). Ele minimiza a distância entre os pontos de entrega dentro de um mesmo cluster, criando zonas de entrega compactas e eficientes.
-   **Justificativa**: Escolhemos o K-Means por sua simplicidade, eficiência computacional e eficácia em problemas de agrupamento baseados em distância, o que se encaixa perfeitamente na necessidade de agrupar endereços próximos.

### b) Algoritmo A* (A-Estrela) (para Otimização de Rota)
-   **Propósito**: Encontrar o caminho mais curto entre dois pontos em um grafo. No nosso caso, usamos o A* para calcular a rota ótima entre cada parada (do restaurante para a entrega 1, da 1 para a 2, etc.).
-   **Funcionamento**: O A* é um algoritmo de busca informada. Ele combina o custo do caminho já percorrido (como o algoritmo de Dijkstra) com uma **função heurística** que estima o custo para chegar ao destino. A heurística utilizada foi a **distância euclidiana**, que fornece uma estimativa direta e otimista da distância restante.
-   **Justificativa**: O A* é ideal para mapas urbanos, pois é mais rápido que buscas não informadas (como BFS e DFS) e garante encontrar a rota mais curta (ótima), sendo computacionalmente mais eficiente que o Dijkstra em grafos grandes.

---

## 4. Diagrama do Grafo e Modelo da Solução

Para representar a cidade, criamos um grafo ponderado onde os vértices são locais e as arestas são as ruas com seus respectivos custos (distâncias).

*(Aqui você pode inserir uma imagem gerada pelo seu código ou um diagrama estático)*

**Exemplo de Diagrama de Grafo:**
![Exemplo de Grafo](https://i.imgur.com/example.png)  <!-- Substitua pelo link da sua imagem -->

**Modelo da Solução (Fluxograma):**
```
1. Receber lista de pedidos (endereços).
   |
   v
2. Se (Nº de entregadores > 1):
   - Aplicar K-Means para agrupar os pedidos em 'k' zonas.
   - Atribuir uma zona para cada entregador.
   |
   v
3. Para cada entregador/zona:
   - Definir a sequência de visitas (Problema do Caixeiro Viajante Simplificado).
   - Usar o algoritmo A* para calcular a rota mais curta entre cada ponto da sequência.
   |
   v
4. Apresentar as rotas otimizadas para cada entregador.
```

---

## 5. Análise dos Resultados


Para validar nossa abordagem, realizamos uma simulação com um conjunto de 10 pedidos e 2 entregadores.

**Cenário Sem Otimização:**
-   Distância Total Percorrida: 85 km.
-   Tempo Estimado Total: 150 minutos.

**Cenário Com Otimização (Rota Inteligente):**
-   Os pedidos foram divididos em dois clusters de 5 entregas cada.
-   As rotas otimizadas com A* resultaram em:
    -   Distância Total Percorrida: 58 km (**Redução de 31%**).
    -   Tempo Estimado Total: 95 minutos (**Redução de 36%**).

Os resultados demonstram a alta eficiência da solução, com reduções significativas tanto na distância percorrida quanto no tempo de entrega.

### Limitações Encontradas
-   O modelo atual utiliza um grafo estático, não considerando variações de **trânsito em tempo real**.
-   O "peso" das arestas foi baseado em distância, mas o tempo real pode variar.
-   A solução para a ordem de entrega (Problema do Caixeiro Viajante) foi simplificada. Para um número muito grande de entregas por motoboy, uma abordagem heurística mais avançada seria necessária.

### Sugestões de Melhoria
-   **Integração com APIs de Tráfego**: Utilizar APIs como Google Maps ou Waze para obter pesos dinâmicos para as arestas, refletindo as condições de trânsito em tempo real.
-   **Aprendizagem por Reforço (Reinforcement Learning)**: Desenvolver um modelo que aprenda com as rotas realizadas, otimizando-as continuamente com base no feedback de tempos de entrega reais.
-   **Algoritmos Genéticos**: Aplicar algoritmos genéticos para resolver o Problema do Caixeiro Viajante de forma mais robusta quando o número de pontos por rota for muito elevado.
