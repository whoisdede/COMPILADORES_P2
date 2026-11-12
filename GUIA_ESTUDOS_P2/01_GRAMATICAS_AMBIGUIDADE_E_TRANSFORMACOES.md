# MÓDULO 01: GRAMÁTICAS, AMBIGUIDADE E TRANSFORMAÇÕES TOP-DOWN

---

## 1. Fundamentos: Derivações, Precedência e Árvores Sintáticas

### 1.1 Gramáticas Livres de Contexto (GLC)
Uma GLC é formalmente uma quádrupla $G = (V, T, P, S)$, onde:
- $V$: Conjunto finito de variáveis ou símbolos não-terminais.
- $T$: Conjunto finito de símbolos terminais (tokens), disjunto de $V$ ($V \cap T = \emptyset$).
- $P$: Conjunto finito de regras de produção da forma $A \to \alpha$, onde $A \in V$ e $\alpha \in (V \cup T)^*$.
- $S \in V$: O símbolo inicial da gramática.

### 1.2 Derivação Mais à Esquerda (*Leftmost*) e Mais à Direita (*Rightmost*)
- **Derivação Mais à Esquerda ($\Rightarrow_{lm}$):** A cada passo da derivação, o não-terminal mais à esquerda da forma sentencial é o escolhido para ser expandido.
- **Derivação Mais à Direita ($\Rightarrow_{rm}$):** A cada passo, o não-terminal mais à direita da forma sentencial é expandido. É a derivação correspondente inversa do processo executado pelos analisadores ascendentes (LR).
- **Forma Sentencial:** Qualquer cadeia $\alpha \in (V \cup T)^*$ tal que $S \Rightarrow^* \alpha$. Se $\alpha \in T^*$, $\alpha$ é uma **sentença** da linguagem $L(G)$.

### 1.3 Precedência e Associatividade em Gramáticas de Expressões
Em compiladores, a árvore sintática reflete diretamente a semântica da avaliação de expressões:
1. **Hierarquia de Precedência:** Quanto mais longe da raiz (mais profundo na árvore) um operador estiver definido, **maior é a sua precedência**.
   - Por exemplo, para que `*` tenha maior precedência que `+`, definimos `E -> E + T | T` e `T -> T * F | F`. O operador `*` só é avaliado abaixo de `T`, logo é executado antes do `+`.
2. **Associatividade:**
   - **Associatividade à Esquerda:** Implementada via **recursão à esquerda** ($A \to A \alpha \mid \beta$). A árvore cresce para a esquerda e para baixo, fazendo operações à esquerda serem avaliadas primeiro.
   - **Associatividade à Direita:** Implementada via **recursão à direita** ($A \to \beta A \mid \beta$) ou operadores unários prefixados ($F \to ! F \mid P$). A árvore cresce para a direita.

---

## 2. Ambiguidade em Gramáticas Livres de Contexto

### 2.1 Definição Formal
Uma gramática $G$ é dita **ambígua** se e somente se existir pelo menos uma sentença $w \in L(G)$ para a qual:
- Existam **duas ou mais árvores sintáticas distintas**, OU
- Existam **duas ou mais derivações mais à esquerda distintas**, OU
- Existam **duas ou mais derivações mais à direita distintas**.

> ⚠️ **Atenção:** Ter uma derivação mais à esquerda e uma derivação mais à direita diferentes NÃO significa que a gramática seja ambígua! Toda gramática tem derivações à esquerda e à direita distintas para sentenças não triviais. A ambiguidade exige **duas derivações do MESMO tipo** (ex: duas $\Rightarrow_{lm}$) gerando a mesma sentença.

### 2.2 Por que analisadores sintáticos determinísticos rejeitam ambiguidade?
Se uma gramática for ambígua:
- **No analisador LL(1):** Haverá mais de uma produção para a mesma entrada na tabela preditiva $M[A, a]$ (conflito de predição).
- **No analisador LR (SLR, LALR, LR(1)):** Haverá conflitos de Deslocamento/Redução (*shift/reduce*) ou Redução/Redução (*reduce/reduce*).

---

## 3. Transformações Gramaticais para Análise Descendente

### 3.1 Eliminação da Recursão à Esquerda Direta (Imediata)
Analisadores preditivos *top-down* (como LL(1) e descendentes recursivos) entram em loop infinito se a gramática contiver recursão à esquerda ($A \Rightarrow^+ A\alpha$).

Dada a produção com recursão à esquerda imediata:
$$A \to A\alpha_1 \mid A\alpha_2 \mid \dots \mid A\alpha_n \mid \beta_1 \mid \beta_2 \mid \dots \mid \beta_m$$
Onde nenhum $\beta_i$ inicia com $A$.

Substitui-se por:
$$\begin{aligned}
A &\to \beta_1 A' \mid \beta_2 A' \mid \dots \mid \beta_m A' \\
A' &\to \alpha_1 A' \mid \alpha_2 A' \mid \dots \mid \alpha_n A' \mid \varepsilon
\end{aligned}$$

### 3.2 Eliminação de Recursão à Esquerda Indireta (Algoritmo Geral)
Se houver ciclos indiretos como $S \to A a$ e $A \to S b$, deve-se aplicar o algoritmo formal:
1. Ordene os não-terminais em uma sequência arbitrária $A_1, A_2, \dots, A_n$.
2. Para $i$ de $1$ até $n$:
   - Para $j$ de $1$ até $i - 1$:
     - Substitua cada produção $A_i \to A_j \gamma$ por $A_i \to \delta_1 \gamma \mid \delta_2 \gamma \mid \dots \mid \delta_k \gamma$, onde $A_j \to \delta_1 \mid \delta_2 \mid \dots \mid \delta_k$ são as produções correntes de $A_j$.
   - Elimine a recursão à esquerda direta imediata entre as produções de $A_i$.

### 3.3 Fatoração à Esquerda (*Left Factoring*)
Quando duas ou mais alternativas de um não-terminal iniciam com a mesma cadeia de símbolos (prefixo comum), o analisador preditivo que olha apenas um símbolo adiante (*lookahead*) não sabe qual caminho escolher.

Dada a regra:
$$A \to \alpha\beta_1 \mid \alpha\beta_2 \mid \dots \mid \alpha\beta_n \mid \gamma_1 \mid \gamma_2 \mid \dots \mid \gamma_m$$
Onde $\alpha$ é o prefixo comum mais longo ($\alpha \neq \varepsilon$) e nenhum $\gamma_j$ inicia com $\alpha$.

Fatora-se extraindo $\alpha$:
$$\begin{aligned}
A &\to \alpha A' \mid \gamma_1 \mid \gamma_2 \mid \dots \mid \gamma_m \\
A' &\to \beta_1 \mid \beta_2 \mid \dots \mid \beta_n
\end{aligned}$$

---

## 4. Resolução Completa dos Exercícios da LISTA 02

### 📌 Questão 01 – Derivações e Árvores Sintáticas
**Gramática dada:**
```
(1) E -> E + T | E - T | T
(2) T -> T * F | F
(3) F -> ( E ) | num
```

#### Expressão 1: `3 + 4 * 5 - 6` (isto é, `num + num * num - num`)
- **Derivação Mais à Esquerda ($\Rightarrow_{lm}$):**
  $$\begin{aligned}
  E &\Rightarrow_{lm} E - T \\
    &\Rightarrow_{lm} E + T - T \\
    &\Rightarrow_{lm} T + T - T \\
    &\Rightarrow_{lm} F + T - T \\
    &\Rightarrow_{lm} \text{num}_3 + T - T \\
    &\Rightarrow_{lm} \text{num}_3 + T * F - T \\
    &\Rightarrow_{lm} \text{num}_3 + F * F - T \\
    &\Rightarrow_{lm} \text{num}_3 + \text{num}_4 * F - T \\
    &\Rightarrow_{lm} \text{num}_3 + \text{num}_4 * \text{num}_5 - T \\
    &\Rightarrow_{lm} \text{num}_3 + \text{num}_4 * \text{num}_5 - F \\
    &\Rightarrow_{lm} \text{num}_3 + \text{num}_4 * \text{num}_5 - \text{num}_6
  \end{aligned}$$

- **Árvore Sintática Estruturada:**
```
            E
         /  |  \
        E   -   T
      / | \      \
     E  +  T      F
     |    /|\     |
     T   T * F   num (6)
     |   |   |
     F   F  num (5)
     |   |
   num  num (4)
   (3)
```
*Análise:* O nó `-` está no nível superior. O nó `+` está à esquerda, garantindo associatividade à esquerda `(3 + (4 * 5)) - 6`. O nó `*` está mais profundo que `+` e `-`, garantindo a multiplicação antes da adição e subtração.

---

#### Expressão 2: `3 * ( 4 - 5 + 6 )`
- **Derivação Mais à Esquerda ($\Rightarrow_{lm}$):**
  $$\begin{aligned}
  E &\Rightarrow_{lm} T \\
    &\Rightarrow_{lm} T * F \\
    &\Rightarrow_{lm} F * F \\
    &\Rightarrow_{lm} \text{num}_3 * F \\
    &\Rightarrow_{lm} \text{num}_3 * ( E ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( E + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( E - T + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( T - T + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( F - T + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( \text{num}_4 - T + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( \text{num}_4 - F + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( \text{num}_4 - \text{num}_5 + T ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( \text{num}_4 - \text{num}_5 + F ) \\
    &\Rightarrow_{lm} \text{num}_3 * ( \text{num}_4 - \text{num}_5 + \text{num}_6 )
  \end{aligned}$$

- **Árvore Sintática Estruturada:**
```
         E
         |
         T
       / | \
      T  *  F
      |   / | \
      F  (  E  )
      |   / | \
     num E  +  T
     (3)/|\    |
       E - T   F
       |   |   |
       T   F  num (6)
       |   |
       F  num (5)
       |
      num (4)
```

---

#### Expressão 3: `3 - ( 4 + 5 * 6 )`
- **Derivação Mais à Esquerda ($\Rightarrow_{lm}$):**
  $$\begin{aligned}
  E &\Rightarrow_{lm} E - T \\
    &\Rightarrow_{lm} T - T \\
    &\Rightarrow_{lm} F - T \\
    &\Rightarrow_{lm} \text{num}_3 - T \\
    &\Rightarrow_{lm} \text{num}_3 - F \\
    &\Rightarrow_{lm} \text{num}_3 - ( E ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( E + T ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( T + T ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( F + T ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( \text{num}_4 + T ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( \text{num}_4 + T * F ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( \text{num}_4 + F * F ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( \text{num}_4 + \text{num}_5 * F ) \\
    &\Rightarrow_{lm} \text{num}_3 - ( \text{num}_4 + \text{num}_5 * \text{num}_6 )
  \end{aligned}$$

- **Árvore Sintática Estruturada:**
```
         E
       / | \
      E  -  T
      |     |
      T     F
      |   / | \
      F  (  E  )
      |   / | \
     num E  +  T
     (3) |    /|\
         T   T * F
         |   |   |
         F   F  num (6)
         |   |
        num num (5)
        (4)
```

---

### 📌 Questão 02 – Gramática para Expressões Booleanas Não Ambígua
**Requisitos:**
- Constantes: `true`, `false`
- Operadores: `||` (menor precedência), `&&` (precedência intermediária), `!` (maior precedência, unário prefixado)
- Delimitadores: `(`, `)`
- A gramática **não pode ser ambígua**.

**Solução Formal:**
Estratificamos a gramática em três níveis hierárquicos:
1. `b_expr` cuida do operador `||` (associativo à esquerda).
2. `b_termo` cuida do operador `&&` (associativo à esquerda).
3. `b_fator` cuida do operador unário prefixado `!` (associativo à direita).
4. `b_prim` cuida dos elementos atômicos: parênteses e constantes booleanas.

```
b_expr  -> b_expr '||' b_termo | b_termo
b_termo -> b_termo '&&' b_fator | b_fator
b_fator -> '!' b_fator | b_prim
b_prim  -> '(' b_expr ')' | true | false
```
*Garantia de Não-Ambiguidade:*
- A recursão à esquerda em `b_expr` e `b_termo` força a associatividade estritamente à esquerda para `||` e `&&`.
- A recursão à direita em `b_fator -> ! b_fator` garante que `! ! true` seja avaliado como `! ( ! true )`.
- O isolamento em níveis impede que uma expressão como `a || b && c` tenha duas interpretações, forçando `(a || (b && c))`.

---

### 📌 Questão 03 – Análise de Ambiguidade das Regras de Seleção
**Regras fornecidas:**
```
CMD       -> if ( exp ) CMD | CASAM-CMD
CASAM-CMD -> if ( exp ) CASAM-CMD else CMD | outro
```

#### Diagnóstico: As regras são AMBÍGUAS!
**Justificativa Teórica Aprofundada:**
Na tentativa clássica de eliminar a ambiguidade do *dangling-else*, a convenção formal (conforme Dragon Book) exige que um comando totalmente casado (*matched*) só contenha comandos igualmente casados em **ambos** os ramos:
$$\text{matched\_stmt} \to \text{if ( exp ) matched\_stmt else matched\_stmt} \mid \text{other}$$

Observe atentamente a regra de `CASAM-CMD` apresentada no enunciado:
$$\text{CASAM-CMD} \to \text{if ( exp ) CASAM-CMD else } \mathbf{CMD} \mid \text{outro}$$
O professor utilizou $\mathbf{CMD}$ (que pode ser aberto/não-casado) logo após o `else`!  
Por causa disso, a categoria `CASAM-CMD` pode terminar com um comando aberto (`if ( exp ) CMD`). Quando essa estrutura é aninhada em um `CMD` externo, a ambiguidade ressurge.

#### Prova Matemática / Contraexemplo Formal:
Considere a sentença válida:
$$w = \text{if ( exp ) if ( exp ) outro else if ( exp ) outro else outro}$$

Podemos construir **duas derivações mais à esquerda distintas**:

**Derivação Mais à Esquerda 1 (O primeiro `if` é expandido via `CMD -> if ( exp ) CMD`):**
$$\begin{aligned}
\text{CMD} &\Rightarrow_{lm} \text{if ( exp ) } \mathbf{CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) } \mathbf{CASAM\text{-}CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) CASAM\text{-}CMD else CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else } \mathbf{CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else } \mathbf{CASAM\text{-}CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else if ( exp ) CASAM\text{-}CMD else CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else if ( exp ) outro else } \mathbf{CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else if ( exp ) outro else outro}
\end{aligned}$$

**Derivação Mais à Esquerda 2 (O primeiro `if` é expandido via `CMD -> CASAM-CMD`):**
$$\begin{aligned}
\text{CMD} &\Rightarrow_{lm} \mathbf{CASAM\text{-}CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) } \mathbf{CASAM\text{-}CMD} \text{ else CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) CASAM\text{-}CMD else CMD else CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else } \mathbf{CMD} \text{ else CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else } \mathbf{if ( exp ) CMD} \text{ else CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else if ( exp ) outro else } \mathbf{CMD} \\
           &\Rightarrow_{lm} \text{if ( exp ) if ( exp ) outro else if ( exp ) outro else outro}
\end{aligned}$$

**Conclusão:** Como a mesma sentença $w$ admite duas derivações mais à esquerda distintas e duas árvores sintáticas distintas, **a gramática é comprovadamente ambígua**.

---

### 📌 Questão 06 – Remoção de Recursão à Esquerda

#### Gramática 1:
```
LEXP     -> ATOMO | LISTA
ATOMO    -> num | id
LISTA    -> ( LEXP-SEQ )
LEXP-SEQ -> LEXP-SEQ LEXP | LEXP
```
- **Análise Não-Terminal por Não-Terminal:**
  - `LEXP`: $ATOMO \mid LISTA$ (Sem recursão).
  - `ATOMO`: $num \mid id$ (Sem recursão).
  - `LISTA`: $( LEXP\text{-}SEQ )$ (Sem recursão).
  - `LEXP-SEQ`: $LEXP\text{-}SEQ\ LEXP \mid LEXP$. **Possui recursão à esquerda imediata!**
    - $A = LEXP\text{-}SEQ$
    - $\alpha_1 = LEXP$
    - $\beta_1 = LEXP$
- **Aplicação da Fórmula:**
  $$\begin{aligned}
  A &\to \beta_1 A' \\
  A' &\to \alpha_1 A' \mid \varepsilon
  \end{aligned}$$
- **Gramática Transformada:**
  ```
  LEXP      -> ATOMO | LISTA
  ATOMO     -> num | id
  LISTA     -> ( LEXP-SEQ )
  LEXP-SEQ  -> LEXP LEXP-SEQ'
  LEXP-SEQ' -> LEXP LEXP-SEQ' | ε
  ```

---

#### Gramática 2:
```
DECL      -> TIPO VAR-LISTA
TIPO      -> int | float
VAR-LISTA -> id , VAR-LISTA | id
```
- **Análise:**
  - `DECL`: Não tem recursão.
  - `TIPO`: Não tem recursão.
  - `VAR-LISTA`: Produz $id\ ,\ VAR\text{-}LISTA \mid id$. O não-terminal recursivo `VAR-LISTA` aparece **no final** da regra. Portanto, trata-se de **recursão à direita**!
- **Conclusão:** **NÃO é necessário remover recursão à esquerda**, pois a gramática NÃO possui recursão à esquerda (nem direta, nem indireta).
- *(Nota de prova):* Se o objetivo futuro fosse torná-la LL(1), ela precisaria apenas de **Fatoração à Esquerda** (pois ambas as produções iniciam com `id`):
  ```
  VAR-LISTA  -> id VAR-LISTA'
  VAR-LISTA' -> , VAR-LISTA | ε
  ```

---

### 📌 Questão 08 – Expressões Booleanas e Árvore de Parsing
**Gramática:**
```
expr  -> expr or termo | termo
termo -> termo and fator | fator
fator -> not fator | (expr) | true | false
```

#### i) Construção da Árvore Gramatical para a sentença:
$$\text{not } ( \text{true or false and true} )$$

```
                         fator
                       /       \
                    not         fator
                              /   |   \
                            (   expr   )
                               /  |  \
                           expr   or   termo
                            |         /  |  \
                          termo   termo and  fator
                            |       |          |
                          fator   fator      true
                            |       |
                          true    false
```
*Passo a passo da redução associada:*
1. `false` vira `fator`, depois `termo`.
2. `true` vira `fator`.
3. `termo and fator` (`false and true`) reduz para `termo`.
4. `true` à esquerda vira `fator`, depois `termo`, depois `expr`.
5. `expr or termo` (`true or (false and true)`) reduz para `expr`.
6. Envolvido por `( expr )`, reduz para `fator`.
7. `not fator` reduz para `fator`.

#### ii) A gramática é ambígua? Justifique sua resposta.
**Resposta:** **NÃO, esta gramática NÃO é ambígua.**
**Justificativa Completa:**
A gramática emprega a técnica formal de estratificação sintática com separação inequívoca de níveis de precedência:
1. **Nível 1 (`expr`):** Trata o operador de menor precedência (`or`). Possui recursão estrita à esquerda (`expr -> expr or termo`), forçando a associatividade à esquerda única.
2. **Nível 2 (`termo`):** Trata o operador de precedência intermediária (`and`). Também possui recursão estrita à esquerda (`termo -> termo and fator`), garantindo que `and` agrupe antes de `or` e associe à esquerda.
3. **Nível 3 (`fator`):** Trata o operador unário de maior precedência (`not`), com recursão estrita à direita (`not fator`), além das unidades atômicas delimitadas por parênteses `(expr)` e literais `true`/`false`.
Não existe qualquer ambiguidade de operador ou precedência na gramática, sendo cada sentença válida mapeada para exatamente uma única árvore sintática.
