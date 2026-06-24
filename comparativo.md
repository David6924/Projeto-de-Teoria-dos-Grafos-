# Comparativo de Algoritmos — Grafos

> Documentação técnica e comparativo dos algoritmos implementados nas Partes 1 e 2.

---

## Parte 1 — Menor Caminho em Grafos Dirigidos com Pesos

### O problema

Dado um grafo dirigido G = (V, E) com pesos nas arestas e dois vértices S (origem) e T (destino), o objetivo é encontrar o caminho de **menor custo total** de S até T.

Formalmente, encontrar um caminho P = (v₀, v₁, …, vₖ) onde v₀ = S e vₖ = T tal que:

```
Custo(P) = Σ w(vᵢ, vᵢ₊₁)  é minimizado
```

---

### 1.1 Dijkstra

**Ideia principal:**
Expande os vértices em ordem crescente de distância estimada a partir da origem. Utiliza uma **fila de prioridade (min-heap)** para selecionar sempre o vértice não visitado com menor distância conhecida. Ao visitar um vértice, relaxa todas as suas arestas de saída.

**Restrição fundamental:** só funciona corretamente quando **todos os pesos são não-negativos**. Um peso negativo pode fazer com que um caminho já "finalizado" seja melhorado posteriormente, violando a suposição central do algoritmo.

**Estratégia de seleção:**
A cada iteração, extrai da heap o vértice u com menor dist[u]. Para cada vizinho v de u, se `dist[u] + w(u,v) < dist[v]`, atualiza dist[v] e insere v na heap.

**Passo a passo:**
```
1. dist[S] = 0; dist[v] = ∞ para todo v ≠ S.
2. Inserir (0, S) na min-heap.
3. Enquanto a heap não estiver vazia:
   a. Extrair (d, u) com menor d.
   b. Se d > dist[u], ignorar (entrada desatualizada).
   c. Para cada (v, w) em adj[u]:
      se dist[u] + w < dist[v]:
         dist[v] = dist[u] + w
         prev[v] = u
         Inserir (dist[v], v) na heap.
4. Reconstruir caminho de T até S usando prev[].
```

**Vantagens:**
- Complexidade O((V + E) log V) com heap binária — muito eficiente na prática.
- Garante a solução ótima quando pesos ≥ 0.
- Amplamente implementado e bem compreendido.

**Desvantagens:**
- Não funciona com pesos negativos.
- Com heap de Fibonacci, chega a O(V log V + E), mas implementação é muito complexa.

**Complexidade:** O((V + E) log V) em tempo; O(V + E) em espaço.

---

### 1.2 Bellman-Ford

**Ideia principal:**
Relaxa **todas as arestas** do grafo repetidamente por V−1 iterações. Ao final, dist[v] contém o menor caminho de S a v. Uma V-ésima passagem serve para **detectar ciclos negativos**: se ainda houver relaxamento possível, existe um ciclo de custo negativo acessível.

**Por que V−1 iterações?** O menor caminho simples em um grafo de V vértices tem no máximo V−1 arestas. Após k iterações, dist[v] contém o custo do melhor caminho usando no máximo k arestas.

**Passo a passo:**
```
1. dist[S] = 0; dist[v] = ∞ para todo v ≠ S.
2. Repetir V−1 vezes:
   Para cada aresta (u, v, w):
      se dist[u] ≠ ∞ e dist[u] + w < dist[v]:
         dist[v] = dist[u] + w
         prev[v] = u
3. Verificar ciclos negativos:
   Para cada aresta (u, v, w):
      se dist[u] + w < dist[v]: → ciclo negativo detectado!
4. Reconstruir caminho de T até S usando prev[].
```

**Vantagens:**
- Funciona com pesos negativos.
- Detecta ciclos negativos.
- Implementação simples — não requer estrutura de dados auxiliar sofisticada.

**Desvantagens:**
- Complexidade O(V · E) — significativamente mais lento que Dijkstra em grafos densos.
- Inviável para grafos muito grandes quando apenas pesos positivos estão presentes.

**Complexidade:** O(V · E) em tempo; O(V) em espaço auxiliar.

---

### 1.3 Tabela Comparativa — Parte 1

| Critério                        | Dijkstra                          | Bellman-Ford                        |
|---------------------------------|-----------------------------------|-------------------------------------|
| **Tipo de abordagem**           | Guloso (expansão por distância)   | Programação dinâmica (relaxamento)  |
| **Complexidade**                | O((V + E) log V)                  | O(V · E)                            |
| **Pesos negativos**             | Não suporta                       | Suporta                             |
| **Detecção de ciclos negativos**| Não                               | Sim                                 |
| **Qualidade da solução**        | Ótima (para pesos ≥ 0)            | Ótima (para grafos sem ciclo negativo) |
| **Estrutura auxiliar**          | Min-heap (fila de prioridade)     | Nenhuma (itera sobre lista de arestas) |
| **Facilidade de implementação** | Moderada                          | Simples                             |
| **Desempenho prático**          | Muito rápido (grafos esparsos)    | Mais lento; aceitável para V, E pequenos |
| **Uso indicado**                | Grafos com todos os pesos ≥ 0     | Grafos com pesos negativos          |

### Critério de seleção automática neste projeto

O `solve_part1()` inspeciona as arestas antes de escolher o algoritmo:

```python
has_negative = any(cost < 0 for _, _, cost in edges)
```

- **Sem pesos negativos** → Dijkstra (`grafo_rede_p.txt`)
- **Com pesos negativos** → Bellman-Ford (`grafo_rede_m.txt`)

---

---

## Parte 2 — Coloração de Grafos

### O problema

A **coloração de vértices** consiste em atribuir uma "cor" (rótulo inteiro) a cada vértice de um grafo de forma que **nenhum par de vértices adjacentes compartilhe a mesma cor**. O menor número de cores necessário é o **número cromático** χ(G).

Formalmente, dado G = (V, E), encontrar `c: V → {1, 2, …, k}` tal que:

```
∀ (u, v) ∈ E : c(u) ≠ c(v)   e   k é minimizado
```

Coloração de grafos é um problema **NP-difícil** no caso geral — por isso heurísticas eficientes como o DSatur têm grande importância prática.

**Aplicações:**

| Domínio | Aplicação |
|---|---|
| Redes Wi-Fi | Alocação de frequências/canais a roteadores vizinhos |
| Compiladores | Alocação de registradores de CPU |
| Escalonamento | Atribuição de horários sem conflito |
| Mapas geográficos | Coloração de regiões adjacentes |

---

### 2.1 Greedy Coloring (Coloração Gulosa Simples)

**Ideia principal:**
Percorre os vértices em ordem fixa (índices naturais) e atribui a cada um a menor cor disponível que não conflite com vizinhos já coloridos. Não há critério de priorização.

**Forma de coloração:**
```
c(v) = min({1, 2, 3, …} \ {c(u) : u ∈ N(v), u já colorido})
```

**Vantagens:**
- Implementação trivial.
- Complexidade O(V + E) — o mais rápido entre os comparados.

**Desvantagens:**
- Extremamente sensível à ordem dos vértices.
- Pode usar até Δ + 1 cores, longe do ótimo.

**Complexidade:** O(V + E) em tempo; O(V) em espaço.

**Qualidade:** Geralmente fraca.

---

### 2.2 Welsh-Powell

**Ideia principal:**
Aprimora o Greedy ordenando os vértices por **grau decrescente** antes de iniciar. Vértices com mais vizinhos são coloridos primeiro, quando há mais liberdade de escolha.

**Estratégia:** Ordem estática por grau — definida antes da execução e não atualizada durante.

**Vantagens:**
- Melhoria consistente sobre o Greedy puro.
- Custo adicional mínimo: apenas O(V log V) para ordenação inicial.

**Desvantagens:**
- Ordem estática: ignora como a coloração evolui.
- Em grafos regulares, ganho sobre o Greedy é praticamente nulo.

**Complexidade:** O(V log V + E) em tempo.

**Qualidade:** Moderada — melhor que o Greedy, inferior ao DSatur em grafos irregulares.

---

### 2.3 DSatur *(algoritmo implementado neste projeto)*

**Ideia principal:**
O **DSatur** (*Degree of Saturation*, Brélaz, 1979) usa **seleção dinâmica por saturação**: a cada iteração escolhe o vértice não colorido com maior número de cores distintas já presentes em sua vizinhança.

**Grau de saturação:**
```
sat(v) = |{c(u) : u ∈ N(v), u já colorido}|
```

**Critério de seleção:**
1. Maior saturação.
2. Desempate: maior grau.
3. Segundo desempate: menor índice.

**Passo a passo:**
```
1. Inicializar: sat(v) = 0 para todo v.
2. Repetir até todos os vértices estarem coloridos:
   a. v* = argmax sat(v) (desempates conforme acima).
   b. c(v*) = menor cor não usada por vizinhos de v*.
   c. Atualizar sat(u) para todo u ∈ N(v*) não colorido.
3. Retornar coloração c e número total de cores k.
```

**Vantagens:**
- Produz colorações muito próximas de χ(G) na prática.
- Adaptativo: reage ao estado corrente da coloração.
- Garante coloração ótima em grafos bipartidos e diversas classes especiais.

**Desvantagens:**
- Mais complexo que Greedy e Welsh-Powell.
- O(V²) pode ser lento para grafos com centenas de milhares de vértices.
- Ainda é heurístico — não garante χ(G) no caso geral.

**Complexidade:** O(V²) no pior caso; O(V + E) em espaço.

**Qualidade:** Boa — frequentemente encontra χ(G) ou χ(G) + 1.

---

### 2.4 Backtracking / Branch and Bound

**Ideia principal:**
Abordagem **exata**: explora recursivamente todas as atribuições possíveis, garantindo encontrar χ(G). O Branch and Bound adiciona podas para descartar ramos que não podem melhorar a solução atual.

**Vantagens:**
- Garante a solução ótima.

**Desvantagens:**
- Complexidade exponencial — inviável para grafos grandes.
- O problema é NP-difícil; intratável na prática para V > 50–100.

**Complexidade:** O(k^V · E) no pior caso.

**Qualidade:** Perfeita — encontra exatamente χ(G).

---

### 2.5 Tabela Comparativa — Parte 2

| Critério                        | Greedy Coloring        | Welsh-Powell           | DSatur                       | Backtracking / B&B            |
|---------------------------------|------------------------|------------------------|------------------------------|-------------------------------|
| **Tipo de abordagem**           | Heurística gulosa      | Heurística gulosa      | Heurística gulosa adaptativa | Exato                         |
| **Complexidade**                | O(V + E)               | O(V log V + E)         | O(V²)                        | O(k^V · E) pior caso          |
| **Qualidade da solução**        | Fraca                  | Moderada               | Boa                          | Ótima — χ(G) garantido        |
| **Facilidade de implementação** | Muito fácil            | Fácil                  | Moderada                     | Difícil                       |
| **Desempenho prático**          | Muito rápido           | Rápido                 | Rápido (grafos médios)       | Lento / inviável para V > 100 |
| **Ordem dos vértices**          | Estática / arbitrária  | Estática (grau ↓)      | Dinâmica (saturação ↓)       | Estática ou heurística        |
| **Garante ótimo?**              | Não                    | Não                    | Não (mas próximo)            | Sim                           |
| **Uso indicado**                | Prototipagem rápida    | Grafos esparsos irreg. | Redes reais, alocação Wi-Fi  | Instâncias pequenas / benchmarks |

---

## Exemplo Prático — DSatur

### Grafo de exemplo

```
Vértices: {0, 1, 2, 3, 4}
Arestas:  {(0,1), (0,2), (0,3), (1,2), (1,4), (2,3), (3,4)}

Adjacências:
  0: [1, 2, 3]   grau = 3
  1: [0, 2, 4]   grau = 3
  2: [0, 1, 3]   grau = 3
  3: [0, 2, 4]   grau = 3
  4: [1, 3]      grau = 2
```

### Execução passo a passo

| Iteração | Vértice escolhido | Motivo                            | Cor atribuída | Atualizações de saturação         |
|----------|-------------------|-----------------------------------|---------------|-----------------------------------|
| 1        | 0                 | sat=0 (empate), grau=3, menor idx | 1             | sat(1)=1, sat(2)=1, sat(3)=1     |
| 2        | 1                 | sat=1 (empate), grau=3, menor idx | 2             | sat(2)=2, sat(4)=1               |
| 3        | 2                 | sat=2 (máxima)                    | 3             | sat(3)=2                         |
| 4        | 3                 | sat=2 (empate), grau=3, menor idx | 2             | sat(4)=2                         |
| 5        | 4                 | único restante                    | 1             | —                                 |

**Resultado:** `0→1, 1→2, 2→3, 3→2, 4→1` | **3 cores** = χ(G) ✓

---

## Conclusão

### Parte 1

Dijkstra e Bellman-Ford resolvem o mesmo problema (menor caminho) com estratégias opostas. Dijkstra é significativamente mais eficiente quando os pesos são não-negativos; Bellman-Ford é o único dos dois capaz de lidar com pesos negativos e detectar ciclos negativos. A seleção automática entre os dois garante que cada instância use o algoritmo mais adequado.

### Parte 2

O DSatur representa o equilíbrio ideal entre qualidade de solução e custo computacional para o problema de coloração: supera o Greedy e o Welsh-Powell na maioria dos grafos práticos ao custo de uma implementação ligeiramente mais elaborada, e produz resultados próximos ao número cromático sem o custo exponencial do Backtracking.

---

*Documentação elaborada como parte de projeto acadêmico em Teoria dos Grafos.*
