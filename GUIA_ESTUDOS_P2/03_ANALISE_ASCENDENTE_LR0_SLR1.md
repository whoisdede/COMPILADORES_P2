# MÓDULO 03: ANÁLISE SINTÁTICA ASCENDENTE LR(0) E SLR(1)

---

## 1. Fundamentos da Análise Ascendente (*Bottom-Up*)

A análise ascendente reconstrói a árvore sintática a partir das folhas (tokens da cadeia de entrada) em direção à raiz (o símbolo inicial da gramática).

```
        Símbolo Inicial (Raiz)
                 ▲
                 │  Reduções sucessivas (A -> β)
                 │  "Derivação mais à direita ao contrário"
                 │
           Tokens de Entrada (Folhas)
```

### 1.1 Conceito de *Handle* (Maçaneta)
Em cada etapa da análise ascendente, uma subcadeia da forma sentencial que casa com o lado direito de uma produção $A \to \beta$ e cuja redução para $A$ representa um passo na **derivação mais à direita ao contrário** é chamada de **handle** (ou maçaneta).
- Se $S \Rightarrow_{rm}^* \alpha A w \Rightarrow_{rm} \alpha \beta w$, então a ocorrência de $\beta$ precedida por $\alpha$ e sucedida por terminais $w$ é o *handle* da forma sentencial $\alpha \beta w$.
- O objetivo central de todo analisador LR é **localizar o handle** e aplicar a redução correta sem hesitação.

### 1.2 O Paradigma *Shift-Reduce* (Deslocar-Reduzir)
O analisador utiliza uma **Pilha** e um buffer de **Entrada**:
1. **Deslocar (*Shift*):** Transfere o próximo token da entrada para o topo da pilha.
2. **Reduzir (*Reduce*):** Quando o topo da pilha contém o *handle* $\beta$ de uma produção $A \to \beta$, substitui-se $\beta$ pelo não-terminal $A$.
3. **Aceitar (*Accept*):** O símbolo inicial $S$ é alcançado e a entrada chegou ao fim ($\$$). Reconhecimento com sucesso!
4. **Erro (*Error*):** Nenhuma transição válida de deslocamento ou redução existe para o estado atual e o token corrente.

---

## 2. A Coleção Canônica de Itens LR(0)

### 2.1 O que é um Item LR(0)?
Um item LR(0) de uma gramática $G$ é uma regra de produção com um ponto ($\cdot$) posicionado em algum lugar do seu lado direito.
O ponto indica **o que já foi reconhecido pelo analisador** (à esquerda do ponto) e **o que ainda se espera reconhecer** (à direita do ponto).

*Exemplo:* A produção $A \to X Y$ produz 3 itens LR(0):
1. $[A \to \cdot X Y]$: Início da produção. Espera-se reconhecer uma cadeia que derive de $X Y$.
2. $[A \to X \cdot Y]$: Já reconhecemos com sucesso uma cadeia gerada por $X$; agora espera-se derivar $Y$.
3. $[A \to X Y \cdot]$: **Item de Redução Completa**. A cadeia inteira $X Y$ já foi reconhecida. O analisador está pronto para reduzir para $A$.

*Nota sobre produções vazias:* Para $A \to \varepsilon$, existe apenas um item: $[A \to \cdot]$. Ele já é imediatamente um item de redução!

### 2.2 A Gramática Aumentada
Para que o analisador saiba com precisão matemática quando parar e aceitar a entrada, acrescenta-se uma nova produção com um novo símbolo inicial $S'$:
$$S' \to S \$$
Quando o analisador atinge a situação de reduzir $S' \to S$, significa que toda a entrada foi consumida com sucesso e a análise é concluída.

---

### 2.3 Operações Fundamentais: FECHAMENTO e DESVIO (GOTO)

#### Operação FECHAMENTO($I$):
Se $I$ é um conjunto de itens da gramática:
1. Inicialmente, todo item de $I$ pertence a $\text{FECHAMENTO}(I)$.
2. Se $[A \to \alpha \cdot B \beta] \in \text{FECHAMENTO}(I)$ (onde o ponto antecede um não-terminal $B$) e $B \to \gamma$ é uma produção da gramática, adiciona-se o item $[B \to \cdot \gamma]$ a $\text{FECHAMENTO}(I)$, caso ele ainda não esteja lá.
3. Aplica-se essa regra reiteradamente até que nenhum novo item possa ser acrescentado.

#### Operação DESVIO($I, X$):
Onde $I$ é um conjunto de itens e $X$ é qualquer símbolo gramatical ($X \in V \cup T$):
$$\text{DESVIO}(I, X) = \text{FECHAMENTO}(\{ [A \to \alpha X \cdot \beta] \mid [A \to \alpha \cdot X \beta] \in I \})$$
Intuitivamente: "Mova o ponto sobre o símbolo $X$ para todos os itens em $I$ que esperavam por $X$, e calcule o fechamento do conjunto resultante".

---

## 3. Construção da Tabela Sintática SLR(1) (*Simple LR*)

A tabela SLR(1) é composta por duas sub-tabelas:
- **AÇÃO[$i, a$]:** Onde $i$ é um estado e $a \in T \cup \{\$\}$. Valores possíveis:
  - $E j$ (*Shift* / Empilhar estado $j$ e avançar entrada);
  - $R k$ (*Reduce* / Reduzir pela produção número $k$);
  - $\text{ACEITA}$ (fim da análise com sucesso);
  - Vazio (erro sintático).
- **DESVIO[$i, A$]:** Onde $i$ é um estado e $A \in V$. Informa para qual estado o autômato transiciona após uma redução para o não-terminal $A$.

### 3.1 Regras de Preenchimento da Tabela SLR(1)
Para cada estado $I_i$ da coleção canônica LR(0):
1. **Deslocamentos (*Shifts*):**  
   Se $[A \to \alpha \cdot a \beta] \in I_i$, onde $a$ é um terminal, e $\text{DESVIO}(I_i, a) = I_j$, defina:
   $$\text{AÇÃO}[i, a] = E j \quad (\text{ou } \text{shift } j)$$
2. **Reduções (*Reduces*):**  
   Se $[A \to \alpha \cdot] \in I_i$ (com $A \neq S'$), sendo esta a produção número $k$:
   $$\text{AÇÃO}[i, a] = R k \quad (\text{ou } \text{reduce } k), \quad \mathbf{\forall a \in \text{FOLLOW}(A)}$$
   > ⚠️ **A Grande Diferença do SLR(1):** Diferente do LR(0) puro (que tenta reduzir em todas as colunas de terminais), o SLR(1) **só reduz nas colunas dos terminais que pertencem a $\text{FOLLOW}(A)$**!
3. **Aceitação (*Accept*):**  
   Se $[S' \to S \cdot] \in I_i$, defina:
   $$\text{AÇÃO}[i, \$] = \text{ACEITA}$$
4. **Desvios de Não-Terminais (*GOTO*):**  
   Para cada não-terminal $A$, se $\text{DESVIO}(I_i, A) = I_j$, defina:
   $$\text{DESVIO}[i, A] = j$$

---

## 4. Conflitos em Analisadores Sintáticos Ascendentes

Quando uma célula da tabela de AÇÃO recebe duas instruções diferentes, a gramática **NÃO é SLR(1)**. Existem dois tipos de conflito:

1. **Conflito Deslocamento/Redução (*Shift/Reduce*):**
   - Ocorre no estado $I_i$ no token terminal $a$ quando:
     - Existe um item $[A \to \alpha \cdot a \beta] \in I_i$ (querendo deslocar com $a$); E
     - Existe um item de redução $[B \to \gamma \cdot] \in I_i$, onde $a \in \text{FOLLOW}(B)$ (querendo reduzir pela regra $B \to \gamma$).
   - O analisador fica sem saber se consome mais um token ou se reduz o que já acumulou.

2. **Conflito Redução/Redução (*Reduce/Reduce*):**
   - Ocorre no estado $I_i$ no token $a$ quando:
     - Existem dois itens de redução distintos $[A \to \alpha \cdot]$ e $[B \to \beta \cdot]$ em $I_i$, e $a \in \text{FOLLOW}(A) \cap \text{FOLLOW}(B)$.
   - O analisador reconheceu uma subcadeia que se encaixa perfeitamente em duas produções diferentes e não tem como decidir qual não-terminal criar.

---

## 5. Estudo de Caso Clássico: Falha do SLR(1) (Imagens `slr1.png`, `slr2.png` e Aula 04)

Considere a gramática de ponteiros e atribuição de valores (*l-values* e *r-values*):
```
(1) S -> L = R
(2) S -> R
(3) L -> * R
(4) L -> id
(5) R -> L
```

### 5.1 Conjuntos FIRST e FOLLOW
- $\text{FIRST}(S) = \text{FIRST}(L) = \text{FIRST}(R) = \{ *, id \}$
- $\text{FOLLOW}(S) = \{ \$ \}$
- $\text{FOLLOW}(L) = \{ =, \$ \}$ (pois $S \Rightarrow L = R$, logo $=$ segue $L$; e $R \Rightarrow L$, logo $\text{FOLLOW}(R) \subseteq \text{FOLLOW}(L)$)
- $\text{FOLLOW}(R) = \{ =, \$ \}$ (pois $S \Rightarrow L = R$, e $\text{FOLLOW}(S) \subseteq \text{FOLLOW}(R) \implies \$$; além disso, na derivação $S \Rightarrow R$, etc., $= \in \text{FOLLOW}(R)$)

### 5.2 O Autômato LR(0) (Conforme `slr1.png`)
```
I0 = FECHAMENTO({[S' -> . S #]})
   [S' -> . S #]
   [S  -> . L = R]
   [S  -> . R]
   [L  -> . * R]
   [L  -> . id]
   [R  -> . L]

Transições de I0:
  com S  -> I1: [S' -> S . #]
  com L  -> I2: [S -> L . = R], [R -> L .]   <--- ESTADO CRÍTICO!
  com R  -> I3: [S -> R .]
  com *  -> I4: [L -> * . R], [R -> . L], [L -> . * R], [L -> . id]
  com id -> I5: [L -> id .]

Transições de I2:
  com =  -> I6: [S -> L = . R], [R -> . L], [L -> . * R], [L -> . id]

Transições de I4:
  com R  -> I7: [L -> * R .]
  com L  -> I8: [R -> L .]
  com *  -> I4
  com id -> I5

Transições de I6:
  com R  -> I9: [S -> L = R .]
  com L  -> I8: [R -> L .]
  com *  -> I4
  com id -> I5
```

### 5.3 A Tabela SLR(1) e o Conflito Fatal (Conforme `slr2.png`)

| Estado | AÇÃO: `=` | AÇÃO: `*` | AÇÃO: `id` | AÇÃO: `$` | DESVIO: $S$ | DESVIO: $L$ | DESVIO: $R$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **0** | | E4 | E5 | | 1 | 2 | 3 |
| **1** | | | | **ACEITA** | | | |
| **2** | **E6 / R5** | | | R5 | | | |
| **3** | | | | R2 | | | |
| **4** | | E4 | E5 | | | 8 | 7 |
| **5** | R4 | | | R4 | | | |
| **6** | | E4 | E5 | | | 8 | 9 |
| **7** | R3 | | | R3 | | | |
| **8** | R5 | | | R5 | | | |
| **9** | | | | R1 | | | |

#### Por que ocorre o Conflito `E6 / R5` no Estado 2?
No Estado 2, temos os itens:
1. $[S \to L \cdot = R]$: Como o ponto está antes do terminal `=`, a regra manda **deslocar para o Estado 6** ($\text{AÇÃO}[2, =] = E6$).
2. $[R \to L \cdot]$: Como o ponto está no final, é uma redução pela produção 5 ($R \to L$). Pela regra do SLR(1), essa redução deve ser colocada em **todas as colunas de $\text{FOLLOW}(R)$**.  
   Como $\text{FOLLOW}(R) = \{ =, \$ \}$, o analisador insere $R5$ na coluna de $=$!

**Resultado:** A célula $\text{AÇÃO}[2, =]$ contém **E6 e R5 simultaneamente**.  
**Conclusão:** A gramática **NÃO é SLR(1)**!

> 💡 **Reflexão Crítica de Teoria:**  
> Existe alguma sentença válida dessa linguagem que comece com $R = \dots$? **NÃO!** Nenhuma forma sentencial válida pode ter $R$ antes de um sinal de igual. A redução $R \to L$ nunca deveria ser permitida se o próximo símbolo fosse $=$.  
> O SLR(1) falhou porque considerou o $\text{FOLLOW}(R)$ global de toda a gramática, sem saber em qual contexto específico o não-terminal $R$ estava sendo analisado. Isso motiva o método mais poderoso: **LR(1) Canônico**!

---

## 6. Algoritmo de Execução do Analisador LR com Pilha

A pilha do analisador LR armazena pares intercalados de símbolos e estados:
$$\text{Pilha} = [s_0, X_1, s_1, X_2, s_2, \dots, X_m, s_m]$$
Onde $s_m$ é o estado atual no topo da pilha.

### 6.1 Algoritmo Passo a Passo (Aula 04, Slide 5)
```
Empilhe o estado inicial 0 na Pilha.
Aponte p para o primeiro token de w$.

Repita para sempre:
    Seja s o estado no topo da Pilha;
    Seja a o símbolo corrente apontado por p;
    
    Se AÇÃO[s, a] == Deslocar s':
        Empilhe o terminal a;
        Empilhe o novo estado s';
        Avance o apontador p;
        
    Senão se AÇÃO[s, a] == Reduzir A -> β (produção k):
        Desempilhe 2 * |β| símbolos da Pilha; // Remove pares (símbolo, estado)
        Seja s' o estado que ficou no novo topo da Pilha;
        Empilhe o não-terminal A;
        Empilhe o estado DESVIO[s', A];
        // O apontador p NÃO se move!
        
    Senão se AÇÃO[s, a] == Aceitar:
        Retorne "Sentença Aceita com Sucesso!";
        
    Senão:
        Gere Erro Sintático ("Token inesperado");
```

### 6.2 Exemplo de Rastreamento da Pilha (Expressão `c + c $`)
Conforme demonstrado nos slides 6 a 15 da Aula 04 para a gramática de expressões:
```
(1) E -> E + T   (2) E -> T   (3) T -> T * F   (4) T -> F   (5) F -> ( E )   (6) F -> c
```

| Passo | Pilha | Entrada | Ação Executada | Justificativa / Próximo Passo |
| :---: | :--- | :--- | :---: | :--- |
| 1 | `0` | `c + c $` | **E5** | Desloca `c` e empilha estado 5 |
| 2 | `0 c 5` | `+ c $` | **R6** | Reduz $F \to c$ (desempilha $2 \times 1 = 2$ elementos: `c` e `5`). Topo fica `0`. $\text{DESVIO}[0, F] = 3$. Empilha $F$ e $3$. |
| 3 | `0 F 3` | `+ c $` | **R4** | Reduz $T \to F$. Desempilha $F 3$. Topo fica `0`. $\text{DESVIO}[0, T] = 2$. Empilha $T$ e $2$. |
| 4 | `0 T 2` | `+ c $` | **R2** | Reduz $E \to T$. Desempilha $T 2$. Topo fica `0`. $\text{DESVIO}[0, E] = 1$. Empilha $E$ e $1$. |
| 5 | `0 E 1` | `+ c $` | **E6** | Desloca `+` e empilha estado 6. |
| 6 | `0 E 1 + 6` | `c $` | **E5** | Desloca `c` e empilha estado 5. |
| 7 | `0 E 1 + 6 c 5` | `$` | **R6** | Reduz $F \to c$. Desempilha $c 5$. Topo fica `6`. $\text{DESVIO}[6, F] = 3$. Empilha $F 3$. |
| 8 | `0 E 1 + 6 F 3` | `$` | **R4** | Reduz $T \to F$. Desempilha $F 3$. Topo fica `6`. $\text{DESVIO}[6, T] = 9$. Empilha $T 9$. |
| 9 | `0 E 1 + 6 T 9` | `$` | **R1** | Reduz $E \to E + T$. Desempilha 6 elementos (`E 1 + 6 T 9`). Topo fica `0`. $\text{DESVIO}[0, E] = 1$. Empilha $E 1$. |
| 10 | `0 E 1` | `$` | **ACEITA** | Entrada esgotada e topo da pilha contém $E 1$. Sucesso! |
