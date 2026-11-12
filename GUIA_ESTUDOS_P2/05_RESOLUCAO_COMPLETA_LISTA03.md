# MÓDULO 05: RESOLUÇÃO COMPLETA DA LISTA 03 – ANÁLISE ASCENDENTE LR

---

## Introdução à Lista 03
A Lista 03 é o núcleo prático da avaliação de análise sintática ascendente do Prof. Ricardo Ferreira Martins. Ela avalia se o estudante é capaz de:
1. Construir autômatos de itens canônicos (LR(0) para SLR(1), LR(1) e LALR(1)).
2. Gerar tabelas de AÇÃO e DESVIO.
3. Detectar e classificar conflitos (*Shift/Reduce* e *Reduce/Reduce*).
4. Julgar a efetividade de cada autômato.
5. Reescrever gramáticas conflitantes para eliminar ambiguidades e torná-las determinísticas sem alterar a linguagem original.

---

## 🧩 Gramática 1: Listas de Expressões Aninhadas

```
(1) E -> ( L )
(2) E -> a
(3) L -> L , E
(4) L -> E
```
*Linguagem gerada:* Identificadores atômicos e listas de expressões entre parênteses separadas por vírgula (ex: `a`, `(a)`, `(a, a)`, `((a, a), a)`).

### 1.1 Conjuntos FIRST e FOLLOW
- Gramática Aumentada: $(0) \; E' \to E \$$
- $\text{FIRST}(E) = \{ (, a \}$
- $\text{FIRST}(L) = \{ (, a \}$
- $\text{FOLLOW}(E') = \{ \$ \}$
- $\text{FOLLOW}(E) = \{ ,, ), \$ \}$
- $\text{FOLLOW}(L) = \{ ,, ) \}$

---

### 1.2 Autômato de Itens LR(0) / SLR(1)
A coleção canônica possui **9 estados**:

- **$I_0 = \text{FECHAMENTO}(\{[E' \to \cdot E \$\}])$:**
  - $[E' \to \cdot E \$]$
  - $[E \to \cdot ( L )]$
  - $[E \to \cdot a]$
  - *Transições:* $E \to I_1, \quad ( \to I_2, \quad a \to I_3$

- **$I_1 = \text{DESVIO}(I_0, E)$:**
  - $[E' \to E \cdot \$]$
  - *Transição:* $\$ \to \text{ACEITA}$

- **$I_2 = \text{DESVIO}(I_0, () = \text{FECHAMENTO}(\{[E \to ( \cdot L )]\})$:**
  - $[E \to ( \cdot L )]$
  - $[L \to \cdot L , E]$
  - $[L \to \cdot E]$
  - $[E \to \cdot ( L )]$
  - $[E \to \cdot a]$
  - *Transições:* $L \to I_4, \quad E \to I_5, \quad ( \to I_2, \quad a \to I_3$

- **$I_3 = \text{DESVIO}(I_0, a)$:**
  - $[E \to a \cdot]$ (Redução $R2$)

- **$I_4 = \text{DESVIO}(I_2, L)$:**
  - $[E \to ( L \cdot )]$
  - $[L \to L \cdot , E]$
  - *Transições:* $) \to I_6, \quad , \to I_7$

- **$I_5 = \text{DESVIO}(I_2, E)$:**
  - $[L \to E \cdot]$ (Redução $R4$)

- **$I_6 = \text{DESVIO}(I_4, ))$:**
  - $[E \to ( L ) \cdot]$ (Redução $R1$)

- **$I_7 = \text{DESVIO}(I_4, ,) = \text{FECHAMENTO}(\{[L \to L , \cdot E]\})$:**
  - $[L \to L , \cdot E]$
  - $[E \to \cdot ( L )]$
  - $[E \to \cdot a]$
  - *Transições:* $E \to I_8, \quad ( \to I_2, \quad a \to I_3$

- **$I_8 = \text{DESVIO}(I_7, E)$:**
  - $[L \to L , E \cdot]$ (Redução $R3$)

---

### 1.3 Tabela Sintática SLR(1) da Gramática 1

| Estado | AÇÃO: `(` | AÇÃO: `)` | AÇÃO: `a` | AÇÃO: `,` | AÇÃO: `$` | DESVIO: $E$ | DESVIO: $L$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **0** | E2 | | E3 | | | 1 | |
| **1** | | | | | **ACEITA** | | |
| **2** | E2 | | E3 | | | 5 | 4 |
| **3** | | R2 | | R2 | R2 | | |
| **4** | | E6 | | E7 | | | |
| **5** | | R4 | | R4 | | | |
| **6** | | R1 | | R1 | R1 | | |
| **7** | E2 | | E3 | | | 8 | |
| **8** | | R3 | | R3 | | | |

*Análise de Efetividade:*  
- **SLR(1):** **EFETIVO (Sem nenhum conflito).**
- **LR(1):** Possui 13 estados e **nenhum conflito**.
- **LALR(1):** A fusão dos 13 estados de mesmo núcleo resulta exatamente nos **9 estados** do SLR(1) e **não possui conflitos**.
- **Veredito:** A gramática é estritamente **SLR(1)**, **LALR(1)** e **LR(1)**!

---

## 🧩 Gramática 2: Sequências com Não-Terminal Anulado

```
(1) S -> a A S
(2) S -> b
(3) A -> c A s
(4) A -> ε
```
*Terminais:* `a`, `b`, `c`, `s`, `$`  
*Não-Terminais:* $S', S, A$

### 2.1 Conjuntos FIRST e FOLLOW
- $\text{FIRST}(S) = \{ a, b \}$
- $\text{FIRST}(A) = \{ c, \varepsilon \}$
- $\text{FOLLOW}(S) = \{ \$ \}$
- $\text{FOLLOW}(A) = \{ a, b, s \}$ (pois em $S \to a A S$, $S$ segue $A$, logo $\text{FIRST}(S) = \{a, b\} \subset \text{FOLLOW}(A)$; e em $A \to c A s$, o terminal $s$ segue $A$).

---

### 2.2 Autômato de Itens LR(0) / SLR(1) (9 Estados)
- **$I_0$:** $\{[S' \to \cdot S \$], [S \to \cdot a A S], [S \to \cdot b]\}$
  - $S \to I_1, \quad a \to I_2, \quad b \to I_3$
- **$I_1$:** $\{[S' \to S \cdot \$]\}$ $\implies$ Aceita em $\$$
- **$I_2 = \text{DESVIO}(I_0, a)$:**
  - $[S \to a \cdot A S]$
  - $[A \to \cdot c A s]$
  - $[A \to \cdot]$ (Item de redução $R4: A \to \varepsilon$)
  - *Atenção aos Conflitos:*
    - Desloca com `c` para $I_5$.
    - Reduz $A \to \varepsilon$ nos terminais de $\text{FOLLOW}(A) = \{ a, b, s \}$.
    - Como $\{c\} \cap \{a, b, s\} = \emptyset$, **NÃO HÁ CONFLITO SHIFT/REDUCE!**
  - *Transições:* $A \to I_4, \quad c \to I_5$
- **$I_3$:** $\{[S \to b \cdot]\} \implies$ Reduz $R2$ em $\text{FOLLOW}(S) = \{\$\}$.
- **$I_4 = \text{DESVIO}(I_2, A)$:**
  - $[S \to a A \cdot S]$
  - $[S \to \cdot a A S]$
  - $[S \to \cdot b]$
  - *Transições:* $S \to I_6, \quad a \to I_2, \quad b \to I_3$
- **$I_5 = \text{DESVIO}(I_2, c)$:**
  - $[A \to c \cdot A s], [A \to \cdot c A s], [A \to \cdot]$
  - *Transições:* $A \to I_7, \quad c \to I_5$
- **$I_6 = \text{DESVIO}(I_4, S)$:**
  - $[S \to a A S \cdot] \implies$ Reduz $R1$ em $\text{FOLLOW}(S) = \{\$\}$.
- **$I_7 = \text{DESVIO}(I_5, A)$:**
  - $[A \to c A \cdot s]$
  - *Transição:* $s \to I_8$
- **$I_8 = \text{DESVIO}(I_7, s)$:**
  - $[A \to c A s \cdot] \implies$ Reduz $R3$ em $\text{FOLLOW}(A) = \{a, b, s\}$.

### 2.3 Tabela Sintática SLR(1) da Gramática 2

| Estado | `a` | `b` | `c` | `s` | `$` | $S$ | $A$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **0** | E2 | E3 | | | | 1 | |
| **1** | | | | | **ACEITA** | | |
| **2** | R4 | R4 | E5 | R4 | | | 4 |
| **3** | | | | | R2 | | |
| **4** | E2 | E3 | | | | 6 | |
| **5** | R4 | R4 | E5 | R4 | | | 7 |
| **6** | | | | | R1 | | |
| **7** | | | | E8 | | | |
| **8** | R3 | R3 | | R3 | | | |

*Análise de Efetividade:*  
- **SLR(1):** **EFETIVO (Sem nenhum conflito).**
- **LALR(1) e LR(1):** Totalmente efetivos e determinísticos.

---

## 🧩 Gramática 3: Parênteses Balanceados Recursivos

```
(1) P -> ε
(2) P -> ( P )
(3) P -> P P
```

### 3.1 Diagnóstico Imediato: Gramática AMBÍGUA!
A regra $P \to P P$ associada a $P \to \varepsilon$ gera ambiguidades severas.  
Para a sentença `()`, podemos derivar:
1. $P \Rightarrow ( P ) \Rightarrow ()$
2. $P \Rightarrow P P \Rightarrow ( P ) P \Rightarrow () \varepsilon \Rightarrow ()$
3. $P \Rightarrow P P \Rightarrow P ( P ) \Rightarrow \varepsilon () \Rightarrow ()$

### 3.2 Consequências no Autômato e Tabelas:
- **SLR(1):** Possui **9 conflitos** (múltiplos conflitos Shift/Reduce e Reduce/Reduce nos estados 0, 1, 2, 3 e 4).
  - No estado inicial $I_0$: conflito entre deslocar `(` e reduzir $P \to \varepsilon$.
  - No estado que reconhece $P$: conflito entre reduzir $P \to P P$ e deslocar `(`.
- **LR(1):** Possui **13 conflitos**.
- **LALR(1):** Possui **9 conflitos**.
- **Veredito:** O autômato **NÃO é efetivo** em nenhuma classe determinística, pois a gramática é inerentemente ambígua!

---

## 🧩 Gramática 4: Parênteses Balanceados Não-Ambígua

```
(1) P -> ε
(2) P -> ( P ) P
```
*Observe a elegância:* Esta é a formulação desambiguada da Gramática 3! O segundo $P$ foi concatenado ao final da regra do parêntese fechado, eliminando a recursão $P \to P P$.

### 4.1 Conjuntos FIRST e FOLLOW
- $\text{FIRST}(P) = \{ (, \varepsilon \}$
- $\text{FOLLOW}(P) = \{ ), \$ \}$

### 4.2 Autômato LR(0) / SLR(1) (6 Estados)
- **$I_0$:**
  - $[P' \to \cdot P \$]$
  - $[P \to \cdot ( P ) P]$
  - $[P \to \cdot]$ (Reduz $R1: P \to \varepsilon$ em $\text{FOLLOW}(P) = \{ ), \$ \}$)
  - *Transições:* $P \to I_1, \quad ( \to I_2$
  - *Sem conflito:* Em `(`, desloca para $I_2$. Reduz apenas em `)` e `$`. Como $\{(\} \cap \{), \$\} = \emptyset$, sem conflitos!
- **$I_1$:** $\{[P' \to P \cdot \$]\} \implies$ Aceita em $\$$
- **$I_2 = \text{DESVIO}(I_0, () = \text{FECHAMENTO}(\{[P \to ( \cdot P ) P]\})$:**
  - $[P \to ( \cdot P ) P]$
  - $[P \to \cdot ( P ) P]$
  - $[P \to \cdot]$ (Reduz $R1$ em $\{ ), \$ \}$)
  - *Transições:* $P \to I_3, \quad ( \to I_2$
- **$I_3 = \text{DESVIO}(I_2, P)$:**
  - $[P \to ( P \cdot ) P]$
  - *Transição:* $) \to I_4$
- **$I_4 = \text{DESVIO}(I_3, )) = \text{FECHAMENTO}(\{[P \to ( P ) \cdot P]\})$:**
  - $[P \to ( P ) \cdot P]$
  - $[P \to \cdot ( P ) P]$
  - $[P \to \cdot]$ (Reduz $R1$ em $\{ ), \$ \}$)
  - *Transições:* $P \to I_5, \quad ( \to I_2$
- **$I_5 = \text{DESVIO}(I_4, P)$:**
  - $[P \to ( P ) P \cdot] \implies$ Reduz $R2$ em $\text{FOLLOW}(P) = \{ ), \$ \}$.

### 4.3 Tabela Sintática SLR(1) da Gramática 4

| Estado | AÇÃO: `(` | AÇÃO: `)` | AÇÃO: `$` | DESVIO: $P$ |
| :---: | :---: | :---: | :---: | :---: |
| **0** | E2 | R1 | R1 | 1 |
| **1** | | | **ACEITA** | |
| **2** | E2 | R1 | R1 | 3 |
| **3** | | E4 | | |
| **4** | E2 | R1 | R1 | 5 |
| **5** | | R2 | R2 | |

*Análise de Efetividade:*  
- **SLR(1):** **100% EFETIVO (Zero conflitos).**
- **LR(1) e LALR(1):** 100% efetivos.
- **Veredito:** Esta gramática é a **solução exata da Questão 03 para resolver os conflitos da Gramática 3**!

---

## 🧩 Gramática 5: Strings com Igual Quantidade de 'a' e 'b'

```
(1) S -> ε
(2) S -> a S b S
(3) S -> b S a S
```

### 5.1 Análise de Ambiguidade
Esta gramática é uma representação clássica ambígua para a linguagem $\{ w \in \{a, b\}^* \mid |w|_a = |w|_b \}$.  
Considere a sentença $w = abab$:
1. $S \Rightarrow a S b S \Rightarrow a \varepsilon b (a S b S) \Rightarrow a b a \varepsilon b \varepsilon = abab$
2. $S \Rightarrow a S b S \Rightarrow a (b S a S) b S \Rightarrow a b \varepsilon a \varepsilon b \varepsilon = abab$

### 5.2 Conflitos no Autômato:
- **SLR(1):** 10 estados, **10 conflitos** de Shift/Reduce nos estados 0, 2 e 3 tanto no token `a` quanto no token `b`.
- **LR(1):** 26 estados, **10 conflitos**.
- **LALR(1):** 10 estados, **6 conflitos**.
- **Veredito:** **NÃO é efetiva**. Os conflitos decorrem da ambiguidade intrínseca da escolha de decomposição da cadeia.

---

## 🧩 Gramática 6: Parênteses e Colchetes Intercalados

```
(1) B  -> ε
(2) B  -> O1 C1
(3) B  -> O2 C2
(4) O1 -> ( B
(5) O1 -> [ B ) B
(6) O2 -> [ B
(7) O2 -> O1 O1
(8) C1 -> ) B
(9) C1 -> ( B ] B
(10) C2 -> ] B
(11) C2 -> C1 C1
```

### 5.3 Análise e Conflitos:
- A presença simultânea de produções vazias ($B \to \varepsilon$) e prefixos sobrepostos entre $O_1$ e $O_2$ (ambos iniciam com `(` e `[`) causa severos conflitos:
  - **SLR(1):** 27 estados, **22 conflitos** (Shift/Reduce e Reduce/Reduce).
  - **LR(1):** 109 estados, **31 conflitos**.
  - **LALR(1):** 27 estados, **14 conflitos**.
- **Veredito:** **NÃO é efetiva** para nenhum dos três métodos sem uma reescrita profunda.

---

## 📋 Resolução Consolidada da Questão 02 (Tabelas e Conflitos)

| Gramática | Estados SLR(1) | Conflitos SLR(1) | Estados LR(1) | Conflitos LR(1) | Estados LALR(1) | Conflitos LALR(1) | É Efetiva? |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **G1** | 9 | **0** | 13 | **0** | 9 | **0** | ✅ **Sim (SLR, LR, LALR)** |
| **G2** | 9 | **0** | 12 | **0** | 9 | **0** | ✅ **Sim (SLR, LR, LALR)** |
| **G3** | 6 | **9** | 10 | **13** | 6 | **9** | ❌ Não (Ambígua) |
| **G4** | 6 | **0** | 10 | **0** | 6 | **0** | ✅ **Sim (SLR, LR, LALR)** |
| **G5** | 10 | **10** | 26 | **10** | 10 | **6** | ❌ Não (Ambígua) |
| **G6** | 27 | **22** | 109 | **31** | 27 | **14** | ❌ Não (Conflitante) |

---

## 🔧 Resolução Consolidada da Questão 03 (Modificação sem Alterar a Linguagem)

### Para a Gramática 3 ($P \to \varepsilon \mid ( P ) \mid P P$):
- **Causa do Conflito:** A recursão $P \to P P$ gera múltiplas árvores para qualquer concatenação de blocos de parênteses.
- **Solução Equivalente:** Transformar na **Gramática 4**:
  $$P \to ( P ) P \mid \varepsilon$$
  Esta gramática gera rigorosamente a mesma linguagem (a linguagem de Dyck de parênteses balanceados), mas é totalmente determinística e livre de conflitos em SLR(1), LALR(1) e LR(1)!

### Para a Gramática 5 ($S \to \varepsilon \mid a S b S \mid b S a S$):
- **Causa do Conflito:** As regras permitem decompor cadeias com mesmo número de $a$ e $b$ de várias maneiras.
- **Solução Equivalente:** Forçar uma decomposição determinística baseada no primeiro retorno à contagem zero:
  $$\begin{aligned}
  S &\to a B S \mid b A S \mid \varepsilon \\
  A &\to a \mid b A A \\
  B &\to b \mid a B B
  \end{aligned}$$
  Onde $A$ gera cadeias com um 'a' a mais, e $B$ gera cadeias com um 'b' a mais. Essa separação elimina a sobreposição de escolhas arbitrárias de prefixo.
