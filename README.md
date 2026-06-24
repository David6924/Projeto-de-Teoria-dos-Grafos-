# Grafos — Algoritmos de Menor Caminho e Coloração

<<<<<<< HEAD
## Integrantes
- Fernando Emídio Belfort do Rego
- David Kelve Oliveira Barbosa
- Marcelo Eduardo Pereira da Silva
- Marcela Rocha Silva
---

=======
>>>>>>> da077e2b9dea9380038de55c7d1399cc81343353
> Implementação acadêmica de algoritmos clássicos de grafos em Python, cobrindo menor caminho em grafos dirigidos ponderados e coloração de grafos não-dirigidos.

---

## Motivação

Problemas de roteamento e alocação de recursos aparecem em redes de telecomunicações, planejamento de infraestrutura e otimização de sistemas distribuídos. Este projeto implementa dois algoritmos fundamentais que modelam esses cenários:

- **Menor caminho** — encontrar a rota de menor custo entre dois pontos em uma rede (ex: roteamento de pacotes, acordos de SLA com custos negativos).
- **Coloração de grafos** — atribuir recursos (frequências Wi-Fi, slots de tempo) a nós de forma que vizinhos nunca compartilhem o mesmo recurso.

A seleção automática de algoritmo — Dijkstra para pesos não-negativos, Bellman-Ford quando há pesos negativos — elimina a necessidade de configuração manual.

---

## Tecnologias

| Camada       | Escolha                        | Motivo                                      |
|--------------|-------------------------------|---------------------------------------------|
| Linguagem    | Python 3.8+                   | Expressividade e portabilidade              |
| Estruturas   | `heapq`, `math` (stdlib)      | Zero dependências externas                  |
| Algoritmos   | Dijkstra, Bellman-Ford, DSatur| Cobertura de casos positivos e negativos    |

Nenhuma biblioteca de terceiros é necessária.

---

## Instalação

### Pré-requisitos

- Python 3.8 ou superior instalado ([download](https://www.python.org/downloads/))

### Passos

```bash
# 1. Clone o repositório
git clone <url-do-repositorio>
cd grafos

# 2. Verifique a versão do Python
python --version   # deve exibir 3.8 ou superior

# 3. Confirme que os arquivos de entrada estão presentes
ls grafo_rede_p.txt grafo_rede_m.txt grafo_wifi_p.txt grafo_wifi_m.txt
```

Não há dependências a instalar — o projeto usa apenas a biblioteca padrão do Python.

---

## Uso

### Parte 1 — Menor Caminho

Detecta automaticamente se o grafo tem pesos negativos e escolhe o algoritmo adequado.

**Formato de entrada** (separado por TAB):

```
<num_vertices>  <num_arestas>
<S>             <T>
<u>  <v>  <custo>
...
```

**Exemplo de entrada (`grafo_rede_p.txt`):**

```
5	6
0	4
0	1	2
0	2	6
1	2	1
1	3	3
2	4	5
3	4	2
```

**Exemplo de saída (`saida_parte1_p.txt`):**

```
ALGORITMO: Dijkstra
JUSTIFICATIVA: O algoritmo de Dijkstra e selecionado pois todos os pesos do grafo sao
nao-negativos, processando vertices em ordem crescente de distancia via fila de
prioridade com complexidade O((V+E) log V), superior ao Bellman-Ford neste cenario.
ROTA: 0 1 3 4
CUSTO: 7
```

**Executar isoladamente:**

```python
from parte1 import solve_part1

solve_part1("grafo_rede_p.txt", "saida_parte1_p.txt")  # → Dijkstra
solve_part1("grafo_rede_m.txt", "saida_parte1_m.txt")  # → Bellman-Ford
```

---

### Parte 2 — Coloração de Grafos

Utiliza o algoritmo **DSatur**: a cada iteração, seleciona o vértice não colorido com maior saturação (número de cores distintas na vizinhança), desempatando pelo grau.

**Formato de entrada** (separado por TAB):

```
<num_vertices>  <num_arestas>
<u>  <v>
...
```

**Exemplo de entrada (`grafo_wifi_p.txt`):**

```
5	7
0	1
1	2
2	3
3	4
4	0
0	2
1	3
```

**Exemplo de saída (`saida_parte2_p.txt`):**

```
ALGORITMO: DSatur
JUSTIFICATIVA: O DSatur e uma heuristica gulosa que prioriza, a cada iteracao, o
vertice nao colorido de maior saturacao, reduzindo conflitos de forma sistematica
e produzindo coloracoes proximas ao numero cromatico em tempo O(V^2).
NUM_CORES: 3
COLORACAO: 0=1 1=2 2=3 3=1 4=2
```

**Executar isoladamente:**

```python
from parte2 import solve_part2

solve_part2("grafo_wifi_p.txt", "saida_parte2_p.txt")
solve_part2("grafo_wifi_m.txt", "saida_parte2_m.txt")
```
