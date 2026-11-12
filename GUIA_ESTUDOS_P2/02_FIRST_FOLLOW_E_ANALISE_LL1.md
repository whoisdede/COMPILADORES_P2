# MÓDULO 02: ALGORITMOS DE FIRST, FOLLOW E ANÁLISE DESCENDENTE LL(1)

---

## 1. Conceito e Definições Formais de FIRST e FOLLOW

O analisador preditivo *top-down* precisa tomar decisões determinísticas: ao olhar o próximo token da entrada (*lookahead*), qual produção $A \to \alpha_1 \mid \alpha_2 \mid \dots$ ele deve expandir?  
As duas funções matemáticas fundamentais que viabilizam essa escolha são **FIRST** e **FOLLOW**.

### 1.1 A Função FIRST
- **Definição:** Seja $\alpha$ uma cadeia qualquer de símbolos da gramática ($\alpha \in (V \cup T)^*$). $\text{FIRST}(\alpha)$ é o conjunto de todos os **símbolos terminais** que podem aparecer no início de cadeias derivadas a partir de $\alpha$.
- Se $\alpha \Rightarrow^* \varepsilon$, então o símbolo vazio $\varepsilon$ também pertence a $\text{FIRST}(\alpha)$.
- Se $a \in T$ (é terminal), $\text{FIRST}(a) = \{ a \}$.
- $\text{FIRST}(\varepsilon) = \{ \varepsilon \}$.

#### Regra de Cálculo de FIRST para uma Cadeia $\alpha = X_1 X_2 \dots X_n$:
1. Adicione a $\text{FIRST}(X_1 X_2 \dots X_n)$ todos os símbolos não-$\varepsilon$ de $\text{FIRST}(X_1)$.
2. Se $\varepsilon \in \text{FIRST}(X_1)$, adicione também todos os símbolos não-$\varepsilon$ de $\text{FIRST}(X_2)$.
3. Continue incluindo $\text{FIRST}(X_i) \setminus \{\varepsilon\}$ para $i = 3, \dots, n$ enquanto todos os símbolos predecessores $X_1, \dots, X_{i-1}$ puderem derivar $\varepsilon$.
4. Se $\varepsilon \in \text{FIRST}(X_j)$ para **todos** os $j = 1, 2, \dots, n$, então adicione $\varepsilon$ a $\text{FIRST}(X_1 X_2 \dots X_n)$.

---

### 1.2 A Função FOLLOW
- **Definição:** Para um símbolo **não-terminal** $A \in V$, $\text{FOLLOW}(A)$ é o conjunto de símbolos **terminais** que podem aparecer imediatamente à direita de $A$ em alguma forma sentencial derivável a partir do símbolo inicial $S$:
  $$\text{FOLLOW}(A) = \{ a \in T \mid S \Rightarrow^* \alpha A a \beta \}$$
- Se $A$ puder ser o símbolo mais à direita em alguma forma sentencial ($S \Rightarrow^* \alpha A$), o marcador de fim de entrada $\$$ pertence a $\text{FOLLOW}(A)$.

#### Regras de Ouro (Slides da Aula e `firstfollow5.png`):
> 🚨 **REGRA DE OURO 1:** A função FIRST é definida para cadeias de símbolos ($\alpha \in (V \cup T)^*$). A função FOLLOW **só é definida para não-terminais** ($A \in V$).  
> 🚨 **REGRA DE OURO 2:** Elementos de FIRST pertencem a $T \cup \{\varepsilon\}$. Elementos de FOLLOW pertencem a $T \cup \{\$\}$. **O símbolo $\varepsilon$ NUNCA pertence ao conjunto FOLLOW!**

---

## 2. O Algoritmo Iterativo de Ponto Fixo (Método das Tabelas)

Em provas do Prof. Ricardo, a melhor e mais segura maneira de demonstrar os cálculos é através da tabela de iterações de ponto fixo.

### 2.1 Algoritmo de Ponto Fixo para FIRST de Não-Terminais
1. Inicialize $\text{FIRST}(A) = \emptyset$ para todo $A \in V$.
2. Repita até que nenhum novo símbolo seja inserido em nenhum conjunto:
   - Para cada produção $A \to Y_1 Y_2 \dots Y_k$:
     - Se a produção for $A \to \varepsilon$, adicione $\varepsilon$ a $\text{FIRST}(A)$.
     - Para cada $i$ de $1$ a $k$:
       - Adicione $\text{FIRST}(Y_i) \setminus \{\varepsilon\}$ a $\text{FIRST}(A)$.
       - Se $\varepsilon \notin \text{FIRST}(Y_i)$, pare e não avance para $Y_{i+1}$.
       - Se $\varepsilon \in \text{FIRST}(Y_i)$ para todo $i=1 \dots k$, adicione $\varepsilon$ a $\text{FIRST}(A)$.

---

### 2.2 Algoritmo de Ponto Fixo para FOLLOW (Baseado em `firstfollow3.png`)
1. Inicialize $\text{FOLLOW}(A) = \emptyset$ para todo $A \neq S$, e $\text{FOLLOW}(S) = \{ \$ \}$.
2. **Passo 1 (Vizinhos imediatos):** Para toda ocorrência de um não-terminal $B$ em uma produção $A \to \alpha B \beta$ (onde $\beta \neq \varepsilon$):
   $$\text{FOLLOW}(B) := \text{FOLLOW}(B) \cup (\text{FIRST}(\beta) \setminus \{\varepsilon\})$$
3. **Passo 2 (Propagação de cauda / Ponto Fixo):**  
   Repita até que não haja mais modificações na tabela:
   - Para cada produção $A \to Y_1 Y_2 \dots Y_n$:
     - Percorra do final para o início ($i = n, n-1, \dots, 1$):
       - Se $Y_i \in V$, então $\text{FOLLOW}(Y_i) := \text{FOLLOW}(Y_i) \cup \text{FOLLOW}(A)$.
       - Se $\varepsilon \notin \text{FIRST}(Y_i)$ ou $Y_i \in T$, pare o recuo para esta produção.

---

## 3. Resolução Completa da Questão 07 da Lista 02

**Gramática fornecida:**
```
(0) S' -> S $
(1) S  -> ε
(2) S  -> X S
(3) B  -> \ begin { WORD }
(4) B  -> \ end { WORD }
(5) X  -> B S E
(6) X  -> { S }
(7) X  -> WORD
(8) X  -> begin
(9) X  -> end
(10) X -> \ WORD
```
*Identificação dos símbolos:*
- Terminais ($T$): `\`, `begin`, `end`, `{`, `}`, `WORD`, `$`. *(E se $E$ for terminal: `E`)*.
- Não-terminais ($V$): $S', S, B, X$. *(E se $E$ for não-terminal representando o fechamento do bloco `\end{WORD}`, conforme convenção usual de linguagens de marcação)*.

### 3.1 Cálculo do FIRST (Tabela de Iterações)

| Não-Terminal | Iteração 0 | Iteração 1 | Iteração 2 (Convergência) | FIRST Final |
| :--- | :---: | :---: | :---: | :---: |
| **$B$** | $\emptyset$ | `{\}` (de $\backslash \dots$) | `{\}` | **$\{\backslash\}$** |
| **$X$** | $\emptyset$ | `{\, {, WORD, begin, end}` | `{\, {, WORD, begin, end}` | **$\{\backslash, \{, \text{WORD}, \text{begin}, \text{end}\}$** |
| **$S$** | $\emptyset$ | `{\, ε}` (de $S \to \varepsilon$) | `{\, {, WORD, begin, end, ε}` | **$\{\backslash, \{, \text{WORD}, \text{begin}, \text{end}, \varepsilon\}$** |
| **$S'$** | $\emptyset$ | $\emptyset$ | `{\, {, WORD, begin, end, $}` | **$\{\backslash, \{, \text{WORD}, \text{begin}, \text{end}, \$\}$** |

*Nota:* No cálculo de $\text{FIRST}(S')$ a partir de $S' \to S \$$, como $\varepsilon \in \text{FIRST}(S)$, o terminal $\$$ é incluído em $\text{FIRST}(S')$.

---

### 3.2 Cálculo do FOLLOW (Passo a Passo Rigoroso)

1. **Inicialização:**
   - $\text{FOLLOW}(S') = \{ \$ \}$
   - $\text{FOLLOW}(S) = \emptyset$
   - $\text{FOLLOW}(B) = \emptyset$
   - $\text{FOLLOW}(X) = \emptyset$

2. **Passo 1 (Regra $A \to \alpha B \beta \implies (\text{FIRST}(\beta) \setminus \{\varepsilon\}) \subseteq \text{FOLLOW}(B)$):**
   - De $S' \to S \$: \quad \$ \in \text{FOLLOW}(S)$.
   - De $S \to X S: \quad (\text{FIRST}(S) \setminus \{\varepsilon\}) \subseteq \text{FOLLOW}(X) \implies \{\backslash, \{, \text{WORD}, \text{begin}, \text{end}\} \subseteq \text{FOLLOW}(X)$.
   - De $X \to B S E:$
     - O que vem depois de $B$ é a cadeia $S E$.
     - $\text{FIRST}(S E) = (\text{FIRST}(S) \setminus \{\varepsilon\}) \cup \text{FIRST}(E)$.  
       Logo, $\{\backslash, \{, \text{WORD}, \text{begin}, \text{end}\} \cup \text{FIRST}(E) \subseteq \text{FOLLOW}(B)$.
     - O que vem depois de $S$ é $E$. Logo, $\text{FIRST}(E) \subseteq \text{FOLLOW}(S)$.
   - De $X \to \{ S \}: \quad \} \in \text{FOLLOW}(S)$.

3. **Passo 2 (Regra de Cauda $A \to \alpha B$ ou $A \to \alpha B \beta$ com $\varepsilon \in \text{FIRST}(\beta) \implies \text{FOLLOW}(A) \subseteq \text{FOLLOW}(B)$):**
   - De $S \to X S$:
     - O símbolo $S$ está no final da cauda: $\text{FOLLOW}(S) \subseteq \text{FOLLOW}(S)$ (trivial).
     - Como $\varepsilon \in \text{FIRST}(S)$, a regra recua para $X$: $\text{FOLLOW}(S) \subseteq \text{FOLLOW}(X)$.
     - Como $\text{FOLLOW}(S)$ contém $\{\$, \}, \text{FIRST}(E)\}$, esses símbolos entram em $\text{FOLLOW}(X)$.
   - De $X \to B S E$:
     - Se $E$ for terminal ou não derivar $\varepsilon$, o recuo para aí: $\text{FOLLOW}(X) \subseteq \text{FOLLOW}(E)$.

#### Tabela Consolidada de FOLLOW:

| Não-Terminal | Símbolos no FOLLOW |
| :--- | :--- |
| **$S'$** | **$\{\$\}$** |
| **$S$** | **$\{\$, \}, \text{FIRST}(E)\}$** *(se $E = \backslash \implies \{\$, \}, \backslash\}$; se $E$ for terminal $\implies \{\$, \}, E\}$)* |
| **$X$** | **$\{\backslash, \{, \text{WORD}, \text{begin}, \text{end}, \}, \$\} \cup \text{FIRST}(E)$** |
| **$B$** | **$\{\backslash, \{, \text{WORD}, \text{begin}, \text{end}\} \cup \text{FIRST}(E)$** |

---

## 4. Análise Sintática Preditiva Descendente LL(1)

### 4.1 O que significa LL(1)?
- **Primeiro L (*Left-to-right*):** Varredura da cadeia de entrada feita estritamente da esquerda para a direita.
- **Segundo L (*Leftmost derivation*):** Constrói a derivação mais à esquerda da sentença.
- **(1) (*1 lookahead token*):** Utiliza apenas um símbolo de consulta antecipada da entrada para decidir inequivocamente qual regra de produção aplicar.

### 4.2 Condição Necessária e Suficiente para uma GLC ser LL(1)
Uma gramática $G$ é LL(1) se e somente se, para quaisquer duas produções distintas $A \to \alpha$ e $A \to \beta$ da gramática:
1. $\text{FIRST}(\alpha) \cap \text{FIRST}(\beta) = \emptyset$ (conjuntos disjuntos de símbolos iniciais).
2. Se $\beta \Rightarrow^* \varepsilon$ ($\varepsilon \in \text{FIRST}(\beta)$), então $\text{FIRST}(\alpha) \cap \text{FOLLOW}(A) = \emptyset$.
3. Não pode haver recursão à esquerda (nem direta nem indireta).
4. A gramática **não pode ser ambígua**.

---

### 4.3 Algoritmo de Construção da Tabela Sintática Preditiva $M[A, a]$
- **Entrada:** Gramática $G = (V, T, P, S)$.
- **Saída:** Matriz bidimensional $M[A, a]$, onde $A \in V$ e $a \in T \cup \{\$\}$.
- **Procedimento:**
  1. Para cada produção $A \to \alpha$ em $P$:
     - Para cada terminal $a \in \text{FIRST}(\alpha)$, adicione $A \to \alpha$ na célula $M[A, a]$.
     - Se $\varepsilon \in \text{FIRST}(\alpha)$, adicione $A \to \alpha$ na célula $M[A, b]$ para cada terminal $b \in \text{FOLLOW}(A)$ (incluindo o marcador $\$$ se $\$ \in \text{FOLLOW}(A)$).
  2. Todas as entradas não preenchidas em $M$ são consideradas **erros sintáticos**.
  3. Se em qualquer célula $M[A, a]$ houver **duas ou mais produções**, a gramática **NÃO É LL(1)**.

---

### 4.4 O Algoritmo do Analisador LL(1) Baseado em Pilha
- **Estruturas:**
  - Cadeia de entrada $w\$$ com ponteiro $p$ apontando para o símbolo corrente $a$.
  - Pilha contendo símbolos gramaticais com base marcada por $\$$.
- **Inicialização:**
  - Empilhar $\$$.
  - Empilhar o símbolo inicial da gramática $S$.
  - Apontar $p$ para o primeiro símbolo de $w\$$.
- **Mecanismo:**
  ```
  Repita:
      Seja X o símbolo no topo da pilha;
      Seja a o símbolo apontado por p;
      Se X é um terminal ou $:
          Se X == a:
              Desempilha X;
              Avança p;
          Senão:
              Gera erro sintático ("esperava X, encontrou a");
      Senão (X é um não-terminal):
          Se M[X, a] == X -> Y1 Y2 ... Yk:
              Desempilha X;
              Empilha Yk, Yk-1, ..., Y1; // ATENÇÃO: ordem inversa! Y1 fica no topo!
          Senão:
              Gera erro sintático ("nenhuma produção em M[X, a]");
  Até que X == $
  ```

---

## 5. Resolução Completa dos Exercícios da LISTA 02

### 📌 Questão 04 – Verificação de Aplicabilidade do Método LL(1)
**Gramática dada:**
```
PROGRAMA -> begin LISTACMD end
LISTACMD -> LISTACMD CMD
          | CMD
CMD      -> do id := num to num begin LISTACMD end
          | read id
          | write EXP
          | id := EXP
EXP      -> EXP + EXP | EXP - EXP | num | id | ( EXP )
```

**Pergunta:** Verifique se essa gramática pode ser aplicada ao método LL(1).

**Resposta e Justificativa Completa:**
**NÃO**, esta gramática **não pode ser aplicada diretamente ao método LL(1)** por três violações fatais:
1. **Recursão à Esquerda Direta em `LISTACMD`:**  
   A produção $\text{LISTACMD} \to \text{LISTACMD CMD} \mid \text{CMD}$ possui recursão à esquerda imediata. Nenhuma gramática com recursão à esquerda pode ser LL(1), pois geraria loop infinito e múltiplos preenchimentos na tabela preditiva.
2. **Recursão à Esquerda Direta em `EXP`:**  
   As produções $\text{EXP} \to \text{EXP} + \text{EXP} \mid \text{EXP} - \text{EXP}$ possuem recursão à esquerda imediata.
3. **Ambiguidade Estrita em `EXP`:**  
   A expressão $\text{num} + \text{num} - \text{num}$ pode ser agrupada como $(\text{num} + \text{num}) - \text{num}$ ou $\text{num} + (\text{num} - \text{num})$. Gramáticas ambíguas **nunca** são LL(1), pois produzem conflitos em $M[\text{EXP}, \text{num}]$.

Para poder ser usada em um parser LL(1), a gramática precisaria ser reformulada (eliminando recursão à esquerda, fatorando prefixos e estratificando expressões aritméticas).

---

### 📌 Questão 05 – Questões Teóricas Fundamentais de LL(1)

#### Pergunta A: "Uma gramática LL(1) pode ser ambígua? Justifique."
**Resposta:** **NÃO.**  
**Justificativa:**  
Por definição, uma gramática é LL(1) se e somente se sua tabela sintática de predição $M[A, a]$ possui **no máximo uma regra de produção em cada entrada**. Se uma gramática fosse ambígua, existiria pelo menos uma sentença $w$ com duas árvores de derivação mais à esquerda distintas. Consequentemente, em algum momento da predição para o mesmo não-terminal $A$ sob o mesmo token de entrada $a$, haveria duas regras aplicáveis válidas, inserindo mais de uma produção na célula $M[A, a]$. Portanto, toda gramática LL(1) é estritamente não ambígua.

#### Pergunta B: "Uma gramática não ambígua precisa ser LL(1)? Justifique."
**Resposta:** **NÃO.**  
**Justificativa:**  
A classe das gramáticas LL(1) é um **subconjunto estrito** das gramáticas não ambíguas ($\text{LL(1)} \subset \text{Não Ambíguas}$). Uma gramática pode ser perfeitamente não ambígua e, ainda assim, não ser LL(1). Exemplos clássicos:
1. **Recursão à esquerda não ambígua:** A gramática clássica $E \to E + T \mid T$, $T \to id$ é totalmente determinística e não ambígua, mas **não é LL(1)** (causa loop infinito no preditivo top-down).
2. **Falta de fatoração com lookahead insuficiente:** A gramática $A \to a b c \mid a b d$ não é ambígua, mas necessita de 3 tokens de lookahead para decidir a regra (é LL(3), mas não LL(1)).
3. **Gramáticas determinísticas pertencentes à família LR(1):** A gramática $S \to L = R \mid R$, $L \to * R \mid id$, $R \to L$ (vista na Aula 04) não é ambígua, mas não é LL(1) porque $FIRST(L=R)$ e $FIRST(R)$ compartilham $\{*, id\}$.

---

### 📌 Questão 09 – Análise de Gramática de Comandos para LL(1)
**Gramática dada:**
```
(1) CMD     -> ATRIB
(2) CMD     -> CHAMADA
(3) CMD     -> outro
(4) ATRIB   -> id := exp
(5) CHAMADA -> id ( exp )
```

**Pergunta:** Essa gramática é LL(1)? Justifique.

**Resposta e Prova Formal:**
**NÃO, a gramática NÃO é LL(1).**

**Demonstração Formal:**
Calculamos os conjuntos FIRST de cada alternativa do não-terminal `CMD`:
$$\begin{aligned}
\text{FIRST}(\text{ATRIB})   &= \text{FIRST}(id := exp) = \{ id \} \\
\text{FIRST}(\text{CHAMADA}) &= \text{FIRST}(id\ ( exp )) = \{ id \} \\
\text{FIRST}(outro)          &= \{ outro \}
\end{aligned}$$

A condição obrigatória para que uma gramática seja LL(1) estipula que as alternativas de um mesmo não-terminal devem ter conjuntos FIRST disjuntos:
$$\text{FIRST}(\text{ATRIB}) \cap \text{FIRST}(\text{CHAMADA}) = \{ id \} \cap \{ id \} = \{ id \} \neq \emptyset$$

Ao construirmos a tabela sintática preditiva $M$, na linha correspondente a `CMD` e na coluna do terminal `id`, teremos:
$$M[\text{CMD}, id] = \{ \text{CMD} \to \text{ATRIB}, \quad \text{CMD} \to \text{CHAMADA} \}$$

Como a tabela preditiva possui uma entrada multiplamente definida (conflito de escolha), **a gramática não é LL(1)**.

*(Solução corretiva por Fatoração à Esquerda):*
```
CMD     -> id SUFIXO | outro
SUFIXO  -> := exp | ( exp )
```
Com essa transformação, $\text{FIRST}(:= exp) = \{ := \}$ e $\text{FIRST}(( exp )) = \{ ( \}$. Como $\{ := \} \cap \{ ( \} = \emptyset$, a gramática passa a ser LL(1)!
