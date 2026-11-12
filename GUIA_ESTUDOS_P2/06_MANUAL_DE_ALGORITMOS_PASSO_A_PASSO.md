# MÓDULO 06: MANUAL DE ALGORITMOS PASSO A PASSO (LIVRO DE RECEITAS DE PROVA)

Este manual é o seu guia procedural definitivo. Para cada algoritmo cobrado na disciplina de Compiladores (UDESC / Prof. Ricardo Ferreira Martins), você encontra:
- **Objetivo e Quando Usar**
- **Entrada e Saída Formais**
- **Passo a Passo Algorítmico Rigoroso**
- **Dica de Prova (Como não errar na mão)**

---

## 🛠️ PARTE 1: TRANSFORMAÇÕES GRAMATICAIS

### Algoritmo 1: Eliminação de Recursão à Esquerda Direta (Imediata)
- **Quando usar:** Quando existir uma produção da forma $A \to A \alpha$. Necessário para análise preditiva top-down (LL(1)).
- **Formato Inicial:**
  $$A \to A\alpha_1 \mid A\alpha_2 \mid \dots \mid A\alpha_n \mid \beta_1 \mid \beta_2 \mid \dots \mid \beta_m$$
  *(onde nenhum $\beta_j$ começa com $A$)*.
- **Passo a Passo de Resolução:**
  1. Identifique todos os sufixos recursivos $\alpha_1, \alpha_2, \dots, \alpha_n$ (o que vem imediatamente após o $A$).
  2. Identifique todas as alternativas não recursivas $\beta_1, \beta_2, \dots, \beta_m$.
  3. Crie um novo não-terminal $A'$.
  4. Reescreva $A$ colocando cada alternativa não recursiva sucedida por $A'$:
     $$A \to \beta_1 A' \mid \beta_2 A' \mid \dots \mid \beta_m A'$$
  5. Escreva as regras de $A'$ com cada sufixo $\alpha_i$ sucedido por $A'$, finalizando com $\varepsilon$:
     $$A' \to \alpha_1 A' \mid \alpha_2 A' \mid \dots \mid \alpha_n A' \mid \varepsilon$$
- 💡 **Dica de Prova:** Se não houver nenhum $\beta$ (ex: $A \to A\alpha$), a linguagem gera apenas cadeias infinitas ou vazias. Deve haver pelo menos uma base $\beta$ para parar a derivação!

---

### Algoritmo 2: Eliminação de Recursão à Esquerda Indireta
- **Quando usar:** Quando a recursão ocorre em ciclo (ex: $S \to A a$, $A \to S b$).
- **Passo a Passo de Resolução:**
  1. Ordene os não-terminais em uma sequência fixa: $A_1, A_2, \dots, A_n$.
  2. Para $i = 1$ até $n$:
     - Para $j = 1$ até $i - 1$:
       - Se houver produções $A_i \to A_j \gamma$, substitua essa ocorrência expandindo todas as produções atuais de $A_j$:
         $$A_j \to \delta_1 \mid \dots \mid \delta_k \implies A_i \to \delta_1 \gamma \mid \dots \mid \delta_k \gamma$$
     - Elimine a recursão à esquerda imediata resultante em $A_i$ usando o Algoritmo 1.
- 💡 **Dica de Prova:** Escolha uma ordem lógica (ex: $S$ primeiro, depois variáveis intermediárias) para minimizar o número de substituições necessárias.

---

### Algoritmo 3: Fatoração à Esquerda (*Left Factoring*)
- **Quando usar:** Quando duas ou mais alternativas do mesmo não-terminal começam com o mesmo prefixo ($\alpha$). Impede que o analisador LL(1) precise adivinhar qual regra usar.
- **Formato Inicial:**
  $$A \to \alpha\beta_1 \mid \alpha\beta_2 \mid \dots \mid \alpha\beta_n \mid \gamma_1 \mid \dots \mid \gamma_m$$
- **Passo a Passo de Resolução:**
  1. Encontre o prefixo comum mais longo $\alpha \neq \varepsilon$ compartilhado por duas ou mais alternativas.
  2. Isole as alternativas não compartilhadas em $\gamma_1, \dots, \gamma_m$.
  3. Crie um novo não-terminal $A'$.
  4. Escreva:
     $$\begin{aligned}
     A &\to \alpha A' \mid \gamma_1 \mid \dots \mid \gamma_m \\
     A' &\to \beta_1 \mid \beta_2 \mid \dots \mid \beta_n
     \end{aligned}$$
  5. Se uma das produções originais era exatamente $\alpha$ (isto é, $\beta_i = \varepsilon$), então coloque $\varepsilon$ como uma das alternativas de $A'$.
  6. Repita se ainda houver prefixos comuns remanescentes.

---

## 🔍 PARTE 2: FIRST, FOLLOW E ANÁLISE LL(1)

### Algoritmo 4: Cálculo do Conjunto FIRST de uma Cadeia $\alpha = X_1 X_2 \dots X_n$
- **Passo a Passo de Resolução:**
  1. Inicialize $F = \emptyset$.
  2. Se $\alpha = \varepsilon$, então $F = \{ \varepsilon \}$. Pare.
  3. Para $i = 1$ até $n$:
     - Adicione todos os símbolos não-$\varepsilon$ de $\text{FIRST}(X_i)$ a $F$.
     - Se $\varepsilon \notin \text{FIRST}(X_i)$, **PARE** e retorne $F$.
     - Se $\varepsilon \in \text{FIRST}(X_i)$, continue o loop para analisar $X_{i+1}$.
  4. Se o loop completou e todos os símbolos $X_1, \dots, X_n$ derivam $\varepsilon$, adicione $\varepsilon$ a $F$.
  5. Retorne $F$.

---

### Algoritmo 5: Cálculo do Conjunto FOLLOW de Todos os Não-Terminais (Ponto Fixo)
- **Regras Fundamentais:**
  - $\text{FOLLOW}(A)$ só contém terminais e $\$$. **NUNCA contém $\varepsilon$!**
- **Passo a Passo de Resolução:**
  1. **Inicialização:**
     - Crie uma linha para cada não-terminal.
     - Coloque $\$$ em $\text{FOLLOW}(S)$ (onde $S$ é o símbolo inicial).
     - Todos os outros não-terminais iniciam vazios ($\emptyset$).
  2. **Passo 1 (Subcadeias imediatas):**
     - Procure onde cada não-terminal $B$ aparece no **lado direito** de qualquer produção $A \to \alpha B \beta$:
     - Calcule $\text{FIRST}(\beta)$.
     - Adicione todo mundo de $(\text{FIRST}(\beta) \setminus \{\varepsilon\})$ ao conjunto $\text{FOLLOW}(B)$.
  3. **Passo 2 (Propagação de Cauda):**
     - Para cada produção $A \to \alpha B$:
       - Adicione tudo de $\text{FOLLOW}(A)$ em $\text{FOLLOW}(B)$.
     - Para cada produção $A \to \alpha B \beta$ onde $\varepsilon \in \text{FIRST}(\beta)$:
       - Como $\beta$ pode desaparecer, adicione tudo de $\text{FOLLOW}(A)$ em $\text{FOLLOW}(B)$.
  4. Repita os passos 2 e 3 até que nenhuma alteração ocorra em nenhuma linha da tabela.

---

### Algoritmo 6: Construção da Tabela Preditiva LL(1) $M[A, a]$
- **Entrada:** Gramática $G$, conjuntos FIRST e FOLLOW.
- **Passo a Passo de Resolução:**
  1. Monte a tabela com linhas = Não-Terminais ($A \in V$) e colunas = Terminais ($a \in T$) mais o marcador de fim ($\$$).
  2. Para cada produção $A \to \alpha$ da gramática:
     - Calcule $\text{FIRST}(\alpha)$.
     - Para cada terminal $a \in \text{FIRST}(\alpha)$ (com $a \neq \varepsilon$):
       - Escreva $A \to \alpha$ na célula $M[A, a]$.
     - Se $\varepsilon \in \text{FIRST}(\alpha)$:
       - Para cada símbolo $b \in \text{FOLLOW}(A)$ (incluindo $\$$ se pertencer ao FOLLOW):
         - Escreva $A \to \alpha$ na célula $M[A, b]$.
  3. **Checagem de LL(1):**
     - Se qualquer célula $M[A, a]$ contiver mais de uma produção $\implies$ **A GRAMÁTICA NÃO É LL(1)!**
     - As células vazias representam erros de sintaxe.

---

### Algoritmo 7: Execução do Analisador LL(1) com Pilha
- **Estruturas:** Pilha e Buffer de Entrada com ponteiro $p$.
- **Passo a Passo:**
  1. Empilhe $\$$; Empilhe $S$. Aponte $p$ para o 1º símbolo de $w\$$.
  2. Repita:
     - Seja $X$ o topo da pilha e $a$ o token atual.
     - **Caso 1 ($X$ é terminal ou $\$$):**
       - Se $X == a == \$ \implies$ **SUCESSO (Cadeia Aceita)!**
       - Se $X == a \neq \$ \implies$ Desempilhe $X$ e avance $p$ ($p++$).
       - Se $X \neq a \implies$ **ERRO SINTÁTICO**.
     - **Caso 2 ($X$ é não-terminal):**
       - Consulte a célula $M[X, a]$.
       - Se $M[X, a]$ contém $X \to Y_1 Y_2 \dots Y_k$:
         - Desempilhe $X$.
         - Se $Y_1 \dots Y_k \neq \varepsilon$, empilhe $Y_k, Y_{k-1}, \dots, Y_1$ (ordem estritamente invertida: $Y_1$ no topo).
       - Se $M[X, a]$ for vazio $\implies$ **ERRO SINTÁTICO**.

---

## 🏗️ PARTE 3: ANÁLISE ASCENDENTE LR(0) E SLR(1)

### Algoritmo 8: Operação FECHAMENTO LR(0) ($\text{FECHAMENTO}(I)$)
- **Passo a Passo:**
  1. Inicialize $J = I$.
  2. Repita até estabilizar:
     - Para cada item $[A \to \alpha \cdot B \beta]$ já presente em $J$:
       - Se $B$ é não-terminal:
         - Para cada produção $B \to \gamma$ da gramática:
           - Adicione o item $[B \to \cdot \gamma]$ a $J$ (se ainda não estiver lá).
  3. Retorne $J$.

---

### Algoritmo 9: Operação DESVIO LR(0) ($\text{DESVIO}(I, X)$ / GOTO)
- **Passo a Passo:**
  1. Encontre todos os itens em $I$ onde o ponto antecede o símbolo $X$ ($[A \to \alpha \cdot X \beta]$).
  2. Avance o ponto sobre $X$ para cada um desses itens, obtendo $[A \to \alpha X \cdot \beta]$.
  3. Calcule o $\text{FECHAMENTO}$ do conjunto resultante.

---

### Algoritmo 10: Construção da Coleção Canônica de Itens LR(0) (Autômato SLR)
- **Passo a Passo:**
  1. Crie a gramática aumentada adicionando $S' \to S \$$.
  2. Defina o estado inicial $I_0 = \text{FECHAMENTO}(\{[S' \to \cdot S \$\}])$.
  3. Defina a lista de estados $C = \{ I_0 \}$.
  4. Repita enquanto novos estados forem descobertos:
     - Para cada estado $I \in C$ e para cada símbolo gramatical $X \in (V \cup T)$:
       - Calcule $J = \text{DESVIO}(I, X)$.
       - Se $J \neq \emptyset$ e $J \notin C$:
         - Adicione $J$ a $C$ como um novo estado.
       - Registre a transição: do estado $I$ com o símbolo $X$ vai para o estado $J$.

---

### Algoritmo 11: Preenchimento da Tabela Sintática SLR(1)
- **Passo a Passo:**
  1. Crie as colunas de **AÇÃO** (para cada terminal $a \in T$ e $\$$) e de **DESVIO** (para cada não-terminal $A \in V$).
  2. Para cada estado $I_i$ da coleção LR(0):
     - **Regra de Deslocamento (*Shift*):**
       - Se $[A \to \alpha \cdot a \beta] \in I_i$ (com $a \in T$) e $\text{DESVIO}(I_i, a) = I_j$:
         - Defina $\text{AÇÃO}[i, a] = E j$ (ou $\text{shift } j$).
     - **Regra de Redução (*Reduce*):**
       - Se $[A \to \alpha \cdot] \in I_i$ (com $A \neq S'$), sendo esta a produção número $k$:
         - Para **todo $a \in \text{FOLLOW}(A)$**, defina $\text{AÇÃO}[i, a] = R k$.
     - **Regra de Aceitação (*Accept*):**
       - Se $[S' \to S \cdot \$] \in I_i$:
         - Defina $\text{AÇÃO}[i, \$] = \text{ACEITA}$.
     - **Regra de Desvio (*Goto*):**
       - Para cada não-terminal $A$, se $\text{DESVIO}(I_i, A) = I_j$:
         - Defina $\text{DESVIO}[i, A] = j$.
  3. **Identificação de Conflitos:**
     - Mais de uma ação na mesma célula $\implies$ Gramática **NÃO é SLR(1)**.

---

### Algoritmo 12: Execução do Analisador LR com Pilha (Shift-Reduce)
- **Estruturas:** Pilha de estados e símbolos intercalados ($s_0 X_1 s_1 \dots X_m s_m$) e Buffer de entrada com ponteiro $p$.
- **Passo a Passo:**
  1. Empilhe o estado inicial $0$. Aponte $p$ para o 1º símbolo de $w\$$.
  2. Repita para sempre:
     - Seja $s$ o estado no topo da pilha e $a$ o token corrente apontado por $p$.
     - Se $\text{AÇÃO}[s, a] == E j$ (*Shift*):
       - Empilhe o terminal $a$; Empilhe o estado $j$; Avance $p$ ($p++$).
     - Se $\text{AÇÃO}[s, a] == R k$ (*Reduce* pela produção $A \to \beta$):
       - Desempilhe $2 \times |\beta|$ elementos da pilha.
       - Seja $s'$ o estado que agora está no topo da pilha.
       - Empilhe o não-terminal $A$.
       - Empilhe o estado $\text{DESVIO}[s', A]$.
       - *(O ponteiro $p$ NÃO se mexe no reduce!)*
     - Se $\text{AÇÃO}[s, a] == \text{ACEITA}$:
       - Pare e retorne "Sentença Reconhecida com Sucesso!".
     - Senão:
       - Pare e retorne **ERRO SINTÁTICO**.

---

## ⚡ PARTE 4: ANÁLISE LR(1) E LALR(1)

### Algoritmo 13: Operação FECHAMENTO LR(1) com Lookahead
- **Item Formato:** $[A \to \alpha \cdot B \beta, \quad a]$
- **Passo a Passo:**
  1. Inicialize $J = I$.
  2. Repita até que nenhum item possa ser adicionado:
     - Para cada item $[A \to \alpha \cdot B \beta, \quad a] \in J$:
       - Se $B$ é não-terminal:
         - Calcule $\text{FIRST}(\beta a)$. *(Lembre-se: se $\beta = \varepsilon \implies \text{FIRST}(\varepsilon a) = \{ a \}$)*.
         - Para cada produção $B \to \gamma$ da gramática:
           - Para cada terminal $b \in \text{FIRST}(\beta a)$:
             - Adicione $[B \to \cdot \gamma, \quad b]$ a $J$ caso ainda não conste.
  3. Retorne $J$.

---

### Algoritmo 14: Fusão de Núcleos para Construção da Tabela LALR(1)
- **Passo a Passo:**
  1. Gere todos os estados da Coleção Canônica LR(1).
  2. Agrupe todos os estados que possuem exatamente os mesmos itens LR(0) (ignorando o lookahead após a vírgula). Esse conjunto idêntico é o **núcleo** (*core*).
  3. Para cada grupo de estados $\{I_{k_1}, I_{k_2}, \dots, I_{k_m}\}$ com mesmo núcleo:
     - Crie um único estado fundido $I_{k_1 k_2 \dots k_m}$.
     - Os itens do estado fundido são os itens do núcleo com a **união dos lookaheads**:
       $$[A \to \alpha \cdot \beta, \quad a_1 \mid a_2 \mid \dots \mid a_r]$$
  4. Redirecione todas as transições para os novos estados fundidos.
  5. Preencha a tabela de AÇÃO: as reduções ocorrem nos lookaheads unificados daquele estado fundido.
