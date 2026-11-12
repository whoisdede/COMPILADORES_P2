# MÓDULO 07: SIMULADO DE PROVA REAL (P2) COM GABARITO DETALHADO

```
========================================================================================
   UNIVERSIDADE DO ESTADO DE SANTA CATARINA – UDESC
   DEPARTAMENTO DE CIÊNCIA DA COMPUTAÇÃO – DCC / CCT
   COM0002 – COMPILADORES | 2ª AVALIAÇÃO PARCIAL (P2)
   PROFESSOR: RICARDO FERREIRA MARTINS
========================================================================================
ALUNO(A): _________________________________________________ DATA: ___/___/______
TEMPO SUGERIDO: 1h40min | VALOR TOTAL: 100 PONTOS | SEM CONSULTA
========================================================================================
```

---

## 📝 CADERNO DE QUESTÕES

### Questão 01 (Valor: 15 Pontos) – Precedência e Árvores Sintáticas
Considere a seguinte gramática livre de contexto para expressões com operadores aritméticos e relacionais:
```
E -> E relop T | T
T -> T + F | F
F -> F * P | P
P -> ( E ) | id | num
```
Onde os operadores relacionais são representados pelo terminal `relop` ($<, >, ==$).
- **a)** (8 pts) Construa a derivação mais à esquerda ($\Rightarrow_{lm}$) e a árvore sintática concreta para a sentença:
  $$id + num * id \ relop \ id$$
- **b)** (7 pts) Com base na estrutura da gramática e na árvore obtida, explique formalmente a ordem de precedência e a associatividade dos operadores `+`, `*` e `relop`.

---

### Questão 02 (Valor: 15 Pontos) – Transformações Gramaticais
Dada a gramática a seguir:
```
S -> S ; CMD | CMD
CMD -> id := EXP | id ( ARG-LIST ) | if EXP then CMD
```
- **a)** (8 pts) Essa gramática pode ser utilizada diretamente em um analisador preditivo descendente LL(1)? Aponte todas as violações existentes.
- **b)** (7 pts) Aplique os algoritmos de **Eliminação de Recursão à Esquerda** e de **Fatoração à Esquerda** necessários para torná-la apta à análise LL(1).

---

### Questão 03 (Valor: 20 Pontos) – Algoritmos de FIRST e FOLLOW
Considere a seguinte gramática livre de contexto:
```
(1) E  -> T E'
(2) E' -> + T E' | ε
(3) T  -> F T'
(4) T' -> * F T' | ε
(5) F  -> ( E ) | id
```
- **a)** (10 pts) Preencha a tabela de iterações de ponto fixo para o cálculo dos conjuntos **FIRST** de todos os não-terminais.
- **b)** (10 pts) Calcule o conjunto **FOLLOW** de cada não-terminal, justificando formalmente cada símbolo adicionado com a regra correspondente.

---

### Questão 04 (Valor: 20 Pontos) – Construção da Tabela LL(1) e Simulação
Utilizando a gramática e os conjuntos obtidos na Questão 03:
- **a)** (10 pts) Construa a **Tabela Sintática Preditiva $M[A, a]$**. Verifique se a gramática é estritamente LL(1).
- **b)** (10 pts) Simule o algoritmo do analisador sintático preditivo não recursivo com **Pilha** para a entrada:
  $$id + id * id \ \$$$
  Apresente as colunas: Passo, Pilha, Entrada e Ação Executada.

---

### Questão 05 (Valor: 20 Pontos) – Análise Ascendente SLR(1)
Dada a gramática:
```
(1) S -> a S b
(2) S -> a b
```
- **a)** (10 pts) Adicione a produção da gramática aumentada ($S' \to S \$$) e construa a coleção canônica completa de **Itens LR(0)** através das operações $\text{FECHAMENTO}$ e $\text{DESVIO}$.
- **b)** (6 pts) Calcule o conjunto $\text{FOLLOW}(S)$ e preencha a **Tabela Sintática SLR(1)** (AÇÃO e DESVIO).
- **c)** (4 pts) O autômato gerado é efetivo ou apresenta conflitos? Justifique.

---

### Questão 06 (Valor: 10 Pontos) – Teoria Avançada: LR(1) vs LALR(1)
Responda de forma precisa e justificada:
- **a)** (5 pts) Ao fundir estados com mesmo núcleo no método LALR(1), é possível introduzir um novo conflito de Deslocamento/Redução (*Shift/Reduce*) que não existia no LR(1) canônico? Demonstre por que sim ou por que não.
- **b)** (5 pts) É possível introduzir um novo conflito de Redução/Redução (*Reduce/Reduce*) na fusão LALR(1)? Explique o mecanismo de como isso pode ocorrer.

---
---

# 🔑 GABARITO OFICIAL COMENTADO E PONTUADO

### Resolução da Questão 01 (15 Pontos)

#### a) Derivação Mais à Esquerda e Árvore (8 Pontos)
- **Derivação $\Rightarrow_{lm}$:**
  $$\begin{aligned}
  E &\Rightarrow_{lm} E \ relop \ T \\
    &\Rightarrow_{lm} T \ relop \ T \\
    &\Rightarrow_{lm} T + F \ relop \ T \\
    &\Rightarrow_{lm} F + F \ relop \ T \\
    &\Rightarrow_{lm} P + F \ relop \ T \\
    &\Rightarrow_{lm} id_1 + F \ relop \ T \\
    &\Rightarrow_{lm} id_1 + F * P \ relop \ T \\
    &\Rightarrow_{lm} id_1 + P * P \ relop \ T \\
    &\Rightarrow_{lm} id_1 + num_2 * P \ relop \ T \\
    &\Rightarrow_{lm} id_1 + num_2 * id_3 \ relop \ T \\
    &\Rightarrow_{lm} id_1 + num_2 * id_3 \ relop \ F \\
    &\Rightarrow_{lm} id_1 + num_2 * id_3 \ relop \ P \\
    &\Rightarrow_{lm} id_1 + num_2 * id_3 \ relop \ id_4
  \end{aligned}$$

- **Árvore Sintática Estruturada:**
```
                     E
                  /  |  \
                 E relop T
               / | \     |
              T  +  F    F
              |    /|\   |
              F   F * P  P
              |   |   |  |
              P   P  id3 id4
              |   |
             id1 num2
```
*(Critério: 4 pts para a derivação passo a passo; 4 pts para a árvore com hierarquia correta).*

#### b) Precedência e Associatividade (7 Pontos)
- **Precedência:**  
  Quanto mais profundo na árvore (mais distante de $E$), maior é a precedência. Portanto:
  $$\mathbf{*} \text{ (mais profundo, nível F)} > \mathbf{+} \text{ (nível T)} > \mathbf{relop} \text{ (nível mais alto, raiz E)}$$
  A multiplicação agrupa primeiro, a adição em seguida, e a comparação relacional é a última a ser avaliada.
- **Associatividade:**  
  Como as produções $E \to E \ relop \ T$, $T \to T + F$ e $F \to F * P$ possuem recursão à esquerda, todos os três operadores são **associativos à esquerda** (as árvores expandem para a esquerda e para baixo).
*(Critério: 4 pts pela precedência com justificativa formal; 3 pts pela associatividade).*

---

### Resolução da Questão 02 (15 Pontos)

#### a) Violações para LL(1) (8 Pontos)
A gramática não pode ser LL(1) devido a duas violações graves:
1. **Recursão à Esquerda Imediata em $S$:** $S \to S \; ; \; CMD \mid CMD$. Um analisador descendente entraria em loop infinito.
2. **Falta de Fatoração à Esquerda em $CMD$:** As produções $CMD \to id := EXP$ e $CMD \to id ( ARG\text{-}LIST )$ iniciam com o mesmo token $id$. O analisador LL(1) tem lookahead de 1 e não sabe qual regra escolher ao ver $id$ na entrada.
*(Critério: 4 pts para cada violação devidamente apontada).*

#### b) Transformação Completa (7 Pontos)
1. **Eliminando a Recursão à Esquerda em $S$:**
   $$\begin{aligned}
   S &\to CMD \; S' \\
   S' &\to ; \; CMD \; S' \mid \varepsilon
   \end{aligned}$$
2. **Fatorando à Esquerda as produções de $CMD$:**
   $$\begin{aligned}
   CMD &\to id \; RESTO \mid if \; EXP \; then \; CMD \\
   RESTO &\to := EXP \mid ( ARG\text{-}LIST )
   \end{aligned}$$
*(Critério: 4 pts para a eliminação da recursão; 3 pts para a fatoração).*

---

### Resolução da Questão 03 (20 Pontos)

#### a) Tabela de Iterações de FIRST (10 Pontos)
| Não-Terminal | Iteração 0 | Iteração 1 | Iteração 2 (Estável) | FIRST Final |
| :---: | :---: | :---: | :---: | :---: |
| **$F$** | $\emptyset$ | `{(, id}` | `{(, id}` | **$\{(, id\}$** |
| **$T'$** | $\emptyset$ | `{*, ε}` | `{*, ε}` | **$\{*, \varepsilon\}$** |
| **$T$** | $\emptyset$ | $\emptyset$ | `{(, id}` | **$\{(, id\}$** |
| **$E'$** | $\emptyset$ | `{+, ε}` | `{+, ε}` | **$\{+, \varepsilon\}$** |
| **$E$** | $\emptyset$ | $\emptyset$ | `{(, id}` | **$\{(, id\}$** |

*(Critério: 2 pts por não-terminal correto com a tabela).*

#### b) Cálculo Justificado de FOLLOW (10 Pontos)
1. $\text{FOLLOW}(E) = \{ \$, ) \}$:
   - $\$$ entra pois $E$ é o símbolo inicial.
   - $)$ entra pois em $F \to ( E )$, o terminal $)$ segue $E$.
2. $\text{FOLLOW}(E') = \text{FOLLOW}(E) = \{ \$, ) \}$:
   - Em $E \to T E'$, $E'$ está na cauda, logo herda $\text{FOLLOW}(E)$.
3. $\text{FOLLOW}(T) = \{ +, \$, ) \}$:
   - Em $E \to T E'$, $E'$ segue $T$. Logo $(\text{FIRST}(E') \setminus \{\varepsilon\}) = \{+\} \subseteq \text{FOLLOW}(T)$.
   - Como $\varepsilon \in \text{FIRST}(E')$, $T$ herda $\text{FOLLOW}(E) = \{\$, )\}$.
4. $\text{FOLLOW}(T') = \text{FOLLOW}(T) = \{ +, \$, ) \}$:
   - Em $T \to F T'$, $T'$ está na cauda, herdando $\text{FOLLOW}(T)$.
5. $\text{FOLLOW}(F) = \{ *, +, \$, ) \}$:
   - Em $T \to F T'$, $T'$ segue $F$. Logo $(\text{FIRST}(T') \setminus \{\varepsilon\}) = \{*\} \subseteq \text{FOLLOW}(F)$.
   - Como $\varepsilon \in \text{FIRST}(T')$, $F$ herda $\text{FOLLOW}(T) = \{+, \$, )\}$.
*(Critério: 2 pts por conjunto FOLLOW com justificativa).*

---

### Resolução da Questão 04 (20 Pontos)

#### a) Tabela Preditiva LL(1) $M$ (10 Pontos)

| Não-Terminal | `id` | `+` | `*` | `(` | `)` | `$` |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **$E$** | $E \to T E'$ | | | $E \to T E'$ | | |
| **$E'$** | | $E' \to + T E'$ | | | $E' \to \varepsilon$ | $E' \to \varepsilon$ |
| **$T$** | $T \to F T'$ | | | $T \to F T'$ | | |
| **$T'$** | | $T' \to \varepsilon$ | $T' \to * F T'$ | | $T' \to \varepsilon$ | $T' \to \varepsilon$ |
| **$F$** | $F \to id$ | | | $F \to ( E )$ | | |

*Conclusão:* Como **nenhuma célula possui mais de uma produção**, a gramática **É ESTRITAMENTE LL(1)**.  
*(Critério: 8 pts para o preenchimento da tabela; 2 pts para a conclusão).*

#### b) Rastreamento da Pilha para `id + id * id $` (10 Pontos)

| Passo | Pilha | Entrada | Ação Executada |
| :---: | :--- | :--- | :--- |
| 1 | `$ E` | `id + id * id $` | Expande $E \to T E'$ |
| 2 | `$ E' T` | `id + id * id $` | Expande $T \to F T'$ |
| 3 | `$ E' T' F` | `id + id * id $` | Expande $F \to id$ |
| 4 | `$ E' T' id` | `id + id * id $` | Casa token `id` (desempilha e avança) |
| 5 | `$ E' T'` | `+ id * id $` | Expande $T' \to \varepsilon$ (desempilha) |
| 6 | `$ E'` | `+ id * id $` | Expande $E' \to + T E'$ |
| 7 | `$ E' T +` | `+ id * id $` | Casa token `+` (desempilha e avança) |
| 8 | `$ E' T` | `id * id $` | Expande $T \to F T'$ |
| 9 | `$ E' T' F` | `id * id $` | Expande $F \to id$ |
| 10 | `$ E' T' id` | `id * id $` | Casa token `id` (desempilha e avança) |
| 11 | `$ E' T'` | `* id $` | Expande $T' \to * F T'$ |
| 12 | `$ E' T' F *` | `* id $` | Casa token `*` (desempilha e avança) |
| 13 | `$ E' T' F` | `id $` | Expande $F \to id$ |
| 14 | `$ E' T' id` | `id $` | Casa token `id` (desempilha e avança) |
| 15 | `$ E' T'` | `$` | Expande $T' \to \varepsilon$ |
| 16 | `$ E'` | `$` | Expande $E' \to \varepsilon$ |
| 17 | `$` | `$` | **ACEITA COM SUCESSO!** |

*(Critério: 10 pts pela simulação completa sem desvios).*

---

### Resolução da Questão 05 (20 Pontos)

#### a) Coleção Canônica LR(0) (10 Pontos)
Gramática aumentada:
(0) $S' \to S \$$  
(1) $S \to a S b$  
(2) $S \to a b$  

- **$I_0 = \text{FECHAMENTO}(\{[S' \to \cdot S \$\}])$:**
  - $[S' \to \cdot S \$]$
  - $[S \to \cdot a S b]$
  - $[S \to \cdot a b]$
  - *Transições:* $S \to I_1, \quad a \to I_2$

- **$I_1 = \text{DESVIO}(I_0, S)$:**
  - $[S' \to S \cdot \$]$ (Aceita em $\$$)

- **$I_2 = \text{DESVIO}(I_0, a)$:**
  - $[S \to a \cdot S b]$
  - $[S \to a \cdot b]$
  - $[S \to \cdot a S b]$
  - $[S \to \cdot a b]$
  - *Transições:* $S \to I_3, \quad b \to I_4, \quad a \to I_2$

- **$I_3 = \text{DESVIO}(I_2, S)$:**
  - $[S \to a S \cdot b]$
  - *Transição:* $b \to I_5$

- **$I_4 = \text{DESVIO}(I_2, b)$:**
  - $[S \to a b \cdot]$ (Reduz $R2$)

- **$I_5 = \text{DESVIO}(I_3, b)$:**
  - $[S \to a S b \cdot]$ (Reduz $R1$)

*(Critério: 10 pts pelo autômato completo de 6 estados com todas as transições).*

#### b) FOLLOW e Tabela Sintática SLR(1) (6 Pontos)
- $\text{FOLLOW}(S') = \{ \$ \}$
- $\text{FOLLOW}(S) = \{ b, \$ \}$ (de $S' \to S \$$ e de $S \to a S b$).

**Tabela SLR(1):**

| Estado | AÇÃO: `a` | AÇÃO: `b` | AÇÃO: `$` | DESVIO: $S$ |
| :---: | :---: | :---: | :---: | :---: |
| **0** | E2 | | | 1 |
| **1** | | | **ACEITA** | |
| **2** | E2 | E4 | | 3 |
| **3** | | E5 | | |
| **4** | | R2 | R2 | |
| **5** | | R1 | R1 | |

*(Critério: 2 pts para o FOLLOW; 4 pts para a tabela preenchida corretamente).*

#### c) Efetividade (4 Pontos)
**O autômato é 100% EFETIVO.**  
Não há nenhuma célula com conflito de Deslocamento/Redução (*shift/reduce*) ou de Redução/Redução (*reduce/reduce*). A gramática é estritamente **SLR(1)**.

---

### Resolução da Questão 06 (10 Pontos)

#### a) Novos Conflitos Shift/Reduce em LALR(1)? (5 Pontos)
**NÃO, é IMPOSSÍVEL.**  
*Justificativa:*  
Dois estados só são fundidos em LALR(1) se possuírem exatamente o mesmo **núcleo** (os mesmos itens LR(0)). A decisão de deslocar (*shift*) depende unicamente da presença de itens da forma $[A \to \alpha \cdot a \beta]$, que é uma propriedade estrita do núcleo.  
Se surgisse um conflito Shift/Reduce no estado fundido no terminal $a$, significaria que um item quisesse deslocar com $a$ e outro quisesse reduzir com lookahead $a$. Mas como ambos os itens já coexistiam nos estados LR(1) originais com o mesmo núcleo e um deles já continha o lookahead $a$, esse conflito já teria que existir obrigatoriamente no autômato LR(1) antes da fusão! Portanto, se a gramática era LR(1) sem conflitos, a fusão LALR(1) nunca gera novos conflitos Shift/Reduce.

#### b) Novos Conflitos Reduce/Reduce em LALR(1)? (5 Pontos)
**SIM, é POSSÍVEL.**  
*Justificativa e Mecanismo:*  
A fusão une os conjuntos de lookahead de estados que possuem núcleos idênticos.  
Considere que existam dois estados LR(1) com o mesmo núcleo:
- Estado 1: possui $[A \to \alpha \cdot, \ a]$ e $[B \to \beta \cdot, \ b]$. Como $a \neq b$, não há conflito em LR(1).
- Estado 2: possui $[A \to \alpha \cdot, \ c]$ e $[B \to \beta \cdot, \ a]$. Como $c \neq a$, também não há conflito em LR(1).
Ao fundir os Estados 1 e 2 em LALR(1), o estado unificado conterá:
- $[A \to \alpha \cdot, \ a \mid c]$
- $[B \to \beta \cdot, \ a \mid b]$  
Observe que agora **ambas as produções $A$ e $B$ desejam reduzir sob o mesmo terminal $a$**! Criou-se um conflito Reduce/Reduce novo que torna a gramática não-LALR(1), mesmo que ela seja perfeitamente LR(1).
