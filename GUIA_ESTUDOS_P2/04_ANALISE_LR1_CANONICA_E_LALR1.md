# MÓDULO 04: ANÁLISE SINTÁTICA LR(1) CANÔNICA E LALR(1)

---

## 1. Por que o LR(1) Canônico é Necessário?

No método SLR(1), a decisão de reduzir utilizando uma produção $A \to \alpha$ é tomada consultando o conjunto global $\text{FOLLOW}(A)$. No entanto, nem todo símbolo terminal em $\text{FOLLOW}(A)$ é um sucessor válido para aquela ocorrência específica de $A$ em determinado ponto da árvore sintática.

O analisador **LR(1)** resolve essa imprecisão incorporando a informação do contexto de derivação diretamente dentro de cada item através de um símbolo de consulta antecipada (*lookahead*).

---

## 2. Itens LR(1) e Operações Canônicas

### 2.1 Estrutura de um Item LR(1)
Um item LR(1) é um par ordenado da forma:
$$[A \to \alpha \cdot \beta, \quad a]$$
Onde:
- $A \to \alpha \beta$ é uma produção da gramática.
- $\cdot$ é o marcador de posição da análise.
- $a \in T \cup \{\$\}$ é o **lookahead** (símbolo terminal ou fim de entrada que pode legitimamente suceder $A$ **nesta derivação específica**).

> 💡 **Significado Operacional:**  
> Se o item estiver na forma $[A \to \alpha \cdot, \quad a]$ (item de redução completa), o analisador **SÓ executará a redução se o próximo token de entrada for exatamente $a$**! Se o próximo token for $b \neq a$, mesmo que $b \in \text{FOLLOW}(A)$, o analisador NÃO reduzirá.

---

### 2.2 Operação FECHAMENTO LR(1)
Dada uma coleção de itens $I$:
```
Repita:
    Para cada item [A -> α . B β, a] em I:
        Para cada produção B -> γ na gramática:
            Para cada terminal b pertencente a FIRST(β a):
                Adicione o item [B -> . γ, b] a I caso ainda não esteja presente.
Até que nenhum novo item possa ser adicionado a I.
```

#### Como calcular $\text{FIRST}(\beta a)$ com precisão:
- **Caso 1: $\beta$ não deriva $\varepsilon$ ($\beta \not\Rightarrow^* \varepsilon$):**  
  $\text{FIRST}(\beta a) = \text{FIRST}(\beta)$. O lookahead herdado $a$ não tem efeito imediato na expansão de $B$.
- **Caso 2: $\beta = \varepsilon$ (o ponto está imediatamente antes de $B$ e não há nada depois):**  
  $\text{FIRST}(\varepsilon a) = \{ a \}$. O lookahead $a$ é repassado diretamente para a nova regra $[B \to \cdot \gamma, a]$!
- **Caso 3: $\beta$ pode derivar $\varepsilon$ ($\varepsilon \in \text{FIRST}(\beta)$):**  
  $\text{FIRST}(\beta a) = (\text{FIRST}(\beta) \setminus \{\varepsilon\}) \cup \{ a \}$.

---

### 2.3 Operação DESVIO LR(1) (GOTO)
A operação de desvio transfere o ponto sobre o símbolo gramatical $X$, **mantendo inalterado o símbolo de lookahead $a$**:
$$\text{DESVIO}(I, X) = \text{FECHAMENTO}(\{ [A \to \alpha X \cdot \beta, \quad a] \mid [A \to \alpha \cdot X \beta, \quad a] \in I \})$$

---

## 3. Como o LR(1) Resolve o Conflito do Exemplo Clássico (Aula 04, Slide 29)

Considere a gramática que falhou no SLR(1):
```
(1) S -> L = R   (2) S -> R   (3) L -> * R   (4) L -> id   (5) R -> L
```
Gramática aumentada: $S' \to \cdot S, \quad \$$

### 3.1 Construção dos Estados Iniciais em LR(1):
- **Estado $I_0$:**
  $$\begin{aligned}
  &[S' \to \cdot S, \quad \$] \\
  &[S \to \cdot L = R, \quad \$] \\
  &[S \to \cdot R, \quad \$] \\
  &[L \to \cdot * R, \quad = \mid \$] \quad \text{(pois vem de } S \to \cdot L = R \implies \text{FIRST}(= R \$) = \{=\}; \text{ e } R \to \cdot L \implies \text{FIRST}(\$) = \{\$\}) \\
  &[L \to \cdot id, \quad = \mid \$] \\
  &[R \to \cdot L, \quad \$] \quad \text{(pois vem de } S \to \cdot R \implies \text{FIRST}(\$) = \{\$\})
  \end{aligned}$$

- **Estado $I_2 = \text{DESVIO}(I_0, L)$:**
  $$\begin{aligned}
  &[S \to L \cdot = R, \quad \$] \\
  &[R \to L \cdot, \quad \$]
  \end{aligned}$$

### 3.2 A Eliminação do Conflito:
Observe o que acontece no Estado $I_2$:
1. O item $[S \to L \cdot = R, \quad \$]$ quer **deslocar** no terminal `=`.
2. O item de redução é $[R \to L \cdot, \quad \mathbf{\$}]$! Seu lookahead é **estritamente o marcador $\$$**, e **NÃO** inclui o terminal `=`!
3. Portanto, quando a entrada for `=`, o analisador sabe que a redução $R \to L$ é inválida naquele contexto e **apenas desloca**!
4. A tabela de AÇÃO fica perfeitamente determinística:
   - $\text{AÇÃO}[2, =] = \text{deslocar } 6$
   - $\text{AÇÃO}[2, \$] = \text{reduzir } R \to L$ (produção 5)
   - $\text{AÇÃO}[2, *] = \text{vazio (erro)}$
   - $\text{AÇÃO}[2, id] = \text{vazio (erro)}$

**Conclusão:** O conflito Shift/Reduce desapareceu! A gramática é **LR(1)**.

---

## 4. O Analisador LALR(1) (*Lookahead LR*)

### 4.1 O Problema da Explosão de Estados do LR(1)
Embora o método LR(1) canônico seja extremamente poderoso, ele gera um número gigantesco de estados. Para linguagens de programação reais como C, Pascal ou Java, o autômato LR(1) pode facilmente ter milhares de estados, tornando a tabela sintática pesada e ineficiente para a memória.

### 4.2 O Conceito de Núcleo (*Core*)
O **núcleo** de um conjunto de itens LR(1) é definido como o conjunto de itens LR(0) obtido **descartando-se completamente a coluna de lookaheads**.

Se dois estados LR(1) $I_j$ e $I_k$ possuem exatamente os mesmos itens com o ponto nas mesmas posições, mudando apenas os símbolos de lookahead, dizemos que $I_j$ e $I_k$ **compartilham o mesmo núcleo**.

### 4.3 O Algoritmo de Fusão LALR(1)
1. Constrói-se a coleção canônica de itens LR(1).
2. Identificam-se todos os grupos de estados que possuem o mesmo núcleo.
3. Para cada grupo de estados com o mesmo núcleo, fundem-se esses estados em um único estado unificado, cujos lookaheads são a união dos lookaheads originais:
   $$[A \to \alpha \cdot \beta, \quad a_1 \mid a_2 \mid \dots \mid a_m]$$
4. As transições de desvio do novo autômato fundido apontam para os estados correspondentes fundidos.

> 📊 **Propriedade Fundamental de Tamanho:**  
> O autômato LALR(1) resultante tem **exatamente o mesmo número de estados do autômato SLR(1) / LR(0)**, preservando a memória compacta, mas com uma capacidade de discriminação de lookaheads muito superior à do SLR(1)!

---

### 4.4 O Teorema dos Conflitos na Fusão LALR(1)

Este é um dos tópicos conceituais mais cobrados em provas de compiladores:

#### 1. Conflitos Deslocamento/Redução (*Shift/Reduce*):
> 🛡️ **TEOREMA 1:** A fusão de estados de mesmo núcleo em LALR(1) **NUNCA introduz novos conflitos Shift/Reduce**.
- **Demonstração Intuitiva:**  
  Para que houvesse um conflito Shift/Reduce no estado fundido em um terminal $a$, seria necessário que um item $[A \to \alpha \cdot a \beta]$ quisesse deslocar e um item de redução $[B \to \gamma \cdot, a]$ quisesse reduzir no mesmo terminal $a$.  
  Como a decisão de deslocar depende puramente da presença de $a$ após o ponto (que é propriedade estrita do núcleo), e a redução depende do lookahead $a$, se o conflito aparecesse no estado fundido, ele já teria que existir em um dos estados LR(1) originais! Como a gramática original era livre de conflitos em LR(1), é impossível surgir conflito Shift/Reduce após a fusão.

#### 2. Conflitos Redução/Redução (*Reduce/Reduce*):
> ⚠️ **TEOREMA 2:** A fusão de estados de mesmo núcleo em LALR(1) **PODE introduzir novos conflitos Reduce/Reduce**.
- **Como isso acontece?**  
  Imagine dois estados LR(1) distintos que compartilham o mesmo núcleo:
  - Estado $I_1$: Contém $[A \to \alpha \cdot, \quad a]$ e $[B \to \beta \cdot, \quad b]$. (Sem conflitos, pois $a \neq b$).
  - Estado $I_2$: Contém $[A \to \alpha \cdot, \quad c]$ e $[B \to \beta \cdot, \quad a]$. (Sem conflitos, pois $c \neq a$).
  Ao fundirmos $I_1$ e $I_2$, o estado resultante terá:
  - $[A \to \alpha \cdot, \quad a \mid c]$
  - $[B \to \beta \cdot, \quad a \mid b]$  
  Observe que **ambas as produções agora desejam reduzir no terminal $a$**! Criou-se um conflito Reduce/Reduce que não existia no LR(1) original.

---

## 5. Hierarquia Completa e Comparação das Gramáticas

A relação de inclusão entre as famílias de linguagens e métodos de análise sintática é representada rigorosamente pela seguinte hierarquia:

$$\mathbf{LL(1) \subset SLR(1) \subset LALR(1) \subset LR(1) \subset GLC\text{ Não-Ambíguas} \subset GLC}$$

```
┌────────────────────────────────────────────────────────────────────────┐
│                        GRAMÁTICAS LIVRES DE CONTEXTO                   │
│                                                                        │
│   ┌──────────────────────────────────────────────────────────────┐     │
│   │                 GLCs NÃO-AMBÍGUAS (DETERMINÍSTICAS)          │     │
│   │                                                              │     │
│   │   ┌──────────────────────────────────────────────────────┐   │     │
│   │   │                      LR(1)                           │   │     │
│   │   │                                                      │   │     │
│   │   │   ┌──────────────────────────────────────────────┐   │   │     │
│   │   │   │                  LALR(1)                     │   │   │     │
│   │   │   │                                              │   │   │     │
│   │   │   │   ┌──────────────────────────────────────┐   │   │   │     │
│   │   │   │   │              SLR(1)                  │   │   │   │     │
│   │   │   │   │                                      │   │   │   │     │
│   │   │   │   │   ┌──────────────────────────────┐   │   │   │   │     │
│   │   │   │   │   │          LL(1)               │   │   │   │   │     │
│   │   │   │   │   └──────────────────────────────┘   │   │   │   │     │
│   │   │   │   └──────────────────────────────────────┘   │   │   │     │
│   │   │   └──────────────────────────────────────────────┘   │   │     │
│   │   └──────────────────────────────────────────────────────┘   │     │
│   └──────────────────────────────────────────────────────────────┘     │
└────────────────────────────────────────────────────────────────────────┘
```

### 5.1 Quadro Comparativo Sintético

| Critério | LL(1) | SLR(1) | LALR(1) | LR(1) |
| :--- | :---: | :---: | :---: | :---: |
| **Direção da Análise** | Top-down (Descendente) | Bottom-up (Ascendente) | Bottom-up (Ascendente) | Bottom-up (Ascendente) |
| **Tipo de Derivação** | Mais à esquerda ($lm$) | Mais à direita ao contrário ($rm^{-1}$) | Mais à direita ao contrário ($rm^{-1}$) | Mais à direita ao contrário ($rm^{-1}$) |
| **Poder Expressivo** | Menor da categoria | Intermediário-baixo | Alto (Prático de Compiladores) | Máximo determinístico |
| **Tamanho da Tabela** | Pequeno ($|V| \times |T|$) | Muito compacto ($N_{LR0}$ estados) | Muito compacto ($N_{LR0}$ estados) | Grande (Explosão de estados) |
| **Decisão de Redução** | $FIRST(\alpha)$ e $FOLLOW(A)$ | $FOLLOW(A)$ global | Lookaheads do núcleo fundido | Lookahead contextual individual |
| **Trata Recursão à Esquerda?** | ❌ Não (Loop infinito) | ✅ Sim | ✅ Sim | ✅ Sim |
| **Suporta Gramáticas Ambíguas?** | ❌ Não | ❌ Não (Conflitos insolúveis) | ⚠️ Com regras de precedência (Bison/Yacc) | ⚠️ Com regras de precedência |

---

## 6. O Uso Prático de Gramáticas Ambíguas em Ferramentas Reais (Yacc/Bison)

Conforme visto na Aula 04 (slide 33) e Aula 07 (slides 5 a 6):
Na prática, gramáticas de linguagens de programação que descrevem expressões aritméticas e estruturas condicionais *if-then-else* são escritas de forma naturalmente ambígua por simplicidade e concisão:
```c
%left '+' '-'
%left '*' '/'
%right '='
%nonassoc LOWER_THAN_ELSE
%nonassoc ELSE
```
1. **No caso do *Dangling-Else*:** O conflito Shift/Reduce entre deslocar o token `else` ou reduzir o comando `if` sem `else` é resolvido instruindo o gerador a **favorecer o deslocamento** (*shift*). Isso associa o `else` ao `if` mais interno e próximo.
2. **Nas Expressões:** As diretivas `%left` e `%right` forçam a redução quando o operador seguinte tem menor precedência, e o deslocamento quando tem maior precedência, eliminando conflitos sem inflar a gramática com não-terminais intermediários.
