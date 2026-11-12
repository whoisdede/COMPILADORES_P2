# 🗺️ MAPA MENTAL MESTRE DE ANÁLISE SINTÁTICA (SÍNTESE FORMAL EM 1 FOLHA A4)
> ⚠️ **DIRETRIZ DE USO:** Este mapa mental contém **exclusivamente definições formais, regras algorítmicas, condições de decisão e fórmulas matemáticas**, sem dados de exemplos específicos, projetado para caber em uma folha A4 e funcionar como sua "cola mental" antes e durante a prova.

```
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 1. TRANSFORMAÇÕES GRAMATICAIS (PREPARAÇÃO TOP-DOWN)                                                    │
├─────────────────────────────────────────────────┬──────────────────────────────────────────────────────┤
│ ELIMINAÇÃO DE RECURSÃO À ESQUERDA IMEDIATA      │ FATORAÇÃO À ESQUERDA (LEFT FACTORING)                │
│ Forma: A → Aα₁ | Aα₂ | ... | β₁ | β₂ | ...      │ Forma: A → αβ₁ | αβ₂ | ... | γ₁ | γ₂ | ...           │
│ Transforma em:                                  │ Transforma em:                                       │
│   A  → β₁A' | β₂A' | ...                        │   A  → αA' | γ₁ | γ₂ | ...                           │
│   A' → α₁A' | α₂A' | ... | ε                    │   A' → β₁ | β₂ | ...                                 │
└─────────────────────────────────────────────────┴──────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 2. FUNÇÕES FUNDAMENTAIS: FIRST E FOLLOW                                                                │
├─────────────────────────────────────────────────┬──────────────────────────────────────────────────────┤
│ FIRST(α)  [α ∈ (V ∪ T)*]                        │ FOLLOW(A)  [A ∈ V apenas! NUNCA contém ε!]           │
│ • Se a ∈ T  ⇒  FIRST(a) = {a}                   │ 1. Inicialização: Coloque $ em FOLLOW(S)             │
│ • Se A → ε  ⇒  ε ∈ FIRST(A)                     │ 2. Regra de Vizinhança: Se A → αBβ, então            │
│ • Para cadeia X₁X₂...Xₙ:                        │      (FIRST(β) \ {ε}) ⊆ FOLLOW(B)                    │
│   - Adicione (FIRST(X₁) \ {ε})                  │ 3. Regra de Cauda (Propagação):                      │
│   - Se ε ∈ FIRST(X₁), adicione (FIRST(X₂) \ {ε})│      Se A → αB  OU  A → αBβ com ε ∈ FIRST(β),        │
│   - Se ε ∈ FIRST(Xᵢ) ∀ i=1..n  ⇒  adicione ε    │      então FOLLOW(A) ⊆ FOLLOW(B)                     │
└─────────────────────────────────────────────────┴──────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 3. ANÁLISE DESCENDENTE: PREDITIVO TABULAR LL(1)                                                        │
├─────────────────────────────────────────────────┬──────────────────────────────────────────────────────┤
│ CONDIÇÃO FORMAL PARA SER LL(1)                  │ CONSTRUÇÃO DA TABELA M[A, a]                         │
│ Para cada A → α | β:                            │ • Para todo terminal a ∈ FIRST(α) \ {ε}:             │
│ 1. FIRST(α) ∩ FIRST(β) = ∅                      │      M[A, a] += (A → α)                              │
│ 2. Se ε ∈ FIRST(α)  ⇒  FIRST(β) ∩ FOLLOW(A) = ∅│ • Se ε ∈ FIRST(α):                                   │
│ 3. Sem recursão à esquerda; Não-ambígua.        │      M[A, b] += (A → α)  ∀ b ∈ FOLLOW(A) (inclui $)  │
├─────────────────────────────────────────────────┴──────────────────────────────────────────────────────┤
│ EXECUÇÃO COM PILHA LL(1):  Base da Pilha = $; Topo inicial = S; Entrada = w$                           │
│ • Se Topo == Token atual == $  ⇒  ACEITA.                                                             │
│ • Se Topo == Token atual ∈ T   ⇒  Desempilha Topo e Avança Entrada.                                  │
│ • Se Topo ∈ V: Consulta M[Topo, Token]. Se A → Y₁..Yₖ  ⇒  Desempilha A, Empilha Yₖ..Y₁ (ordem inversa)│
│ • Se M[Topo, Token] == Vazio   ⇒  ERRO SINTÁTICO.                                                      │
└────────────────────────────────────────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 4. ANÁLISE ASCENDENTE: ITENS, OPERAÇÕES E TABELA SLR(1)                                                │
├─────────────────────────────────────────────────┬──────────────────────────────────────────────────────┤
│ GRAMÁTICA AUMENTADA: Adicione S' → S$           │ OPERAÇÃO FECHAMENTO(I) [LR(0)]                       │
│ ITENS LR(0): Regra com marcador de progresso (•)│ 1. I ⊆ FECHAMENTO(I)                                 │
│ • [A → •αβ] : Início da expectativa             │ 2. Se [A → α•Bβ] ∈ I e B → γ,                        │
│ • [A → α•β] : α reconhecido, aguarda β          │    adicione [B → •γ] a FECHAMENTO(I)                 │
│ • [A → αβ•] : Redução completa                  ├──────────────────────────────────────────────────────┤
│ • [A → •]   : Redução imediata de vazio (A → ε) │ OPERAÇÃO DESVIO(I, X) = FECHAMENTO({[A→αX•β]|[A→α•Xβ]∈I})│
├─────────────────────────────────────────────────┴──────────────────────────────────────────────────────┤
│ PREENCHIMENTO DA TABELA SLR(1)                                                                         │
│ • AÇÃO: Se [A → α•aβ] ∈ Iᵢ e DESVIO(Iᵢ, a) = Iⱼ, com a ∈ T      ⇒  AÇÃO[i, a] = Desloca j (Ej)        │
│ • AÇÃO: Se [A → α•] ∈ Iᵢ (regra k, A ≠ S')                     ⇒  AÇÃO[i, a] = Reduz k (Rk) ∀ a∈FOLLOW(A)│
│ • AÇÃO: Se [S' → S•$] ∈ Iᵢ                                     ⇒  AÇÃO[i, $] = ACEITA                  │
│ • DESVIO: Se DESVIO(Iᵢ, A) = Iⱼ, com A ∈ V                     ⇒  DESVIO[i, A] = j                     │
├────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ DIAGNÓSTICO DE CONFLITOS NO MESMO ESTADO:                                                             │
│ • Conflito Shift/Reduce : Item [A → α•aβ]  E  Item [B → γ•] com a ∈ FOLLOW(B).                         │
│ • Conflito Reduce/Reduce: Itens [A → α•] e [B → β•] com FOLLOW(A) ∩ FOLLOW(B) ≠ ∅.                    │
└────────────────────────────────────────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 5. ANÁLISE LR(1) CANÔNICA E LALR(1)                                                                    │
├─────────────────────────────────────────────────┬──────────────────────────────────────────────────────┤
│ ITENS LR(1): [A → α•β, a] onde a ∈ T ∪ {$}      │ OPERAÇÃO FECHAMENTO LR(1)                            │
│ • Redução [A → α•, a] só ocorre no lookahead a! │ Se [A → α•Bβ, a] ∈ I e B → γ:                        │
│ • Elimina falsas reduções do FOLLOW do SLR(1).  │    Adicione [B → •γ, b] para todo b ∈ FIRST(βa)      │
├─────────────────────────────────────────────────┴──────────────────────────────────────────────────────┤
│ LALR(1) (LOOKAHEAD LR)                                                                                 │
│ • Núcleo (Core): Conjunto de itens LR(0) descartando-se os lookaheads.                                 │
│ • Fusão: Estados LR(1) com mesmo núcleo são fundidos em um só, unindo seus lookaheads.                 │
│ • NÚMERO DE ESTADOS: |LALR(1)| == |SLR(1)| << |LR(1)|                                                  │
│ • TEOREMA DOS CONFLITOS EM LALR(1):                                                                    │
│   - NUNCA introduz novos conflitos Shift/Reduce (preservados do LR(1)).                                │
│   - PODE introduzir novos conflitos Reduce/Reduce (quando lookaheads disjuntos colidem na fusão).      │
└────────────────────────────────────────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 6. EXECUÇÃO DO ANALISADOR ASCENDENTE COM PILHA (SHIFT-REDUCE)                                          │
├────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Estado inicial 0 na Pilha. Aponte p para o 1º símbolo de w$.                                           │
│ Sendo s o estado no topo da Pilha e a o símbolo corrente da entrada:                                   │
│ • Se AÇÃO[s, a] == Desloca j (Ej) : Empilha a; Empilha j; Avança ponteiro p.                          │
│ • Se AÇÃO[s, a] == Reduz k (A → β): Desempilha 2 × |β| símbolos da Pilha;                              │
│                                     Seja s' o novo estado no topo; Empilha A; Empilha DESVIO[s', A].   │
│                                     (O ponteiro de entrada p NÃO avança no reduce!)                    │
│ • Se AÇÃO[s, a] == ACEITA         : Sentença Reconhecida com Sucesso.                                  │
│ • Se AÇÃO[s, a] == VAZIO          : ERRO SINTÁTICO.                                                    │
└────────────────────────────────────────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 7. HIERARQUIA DE PODER EXPRESSIVO DETERMINÍSTICO                                                       │
├────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│          LL(1)  ⊂  SLR(1)  ⊂  LALR(1)  ⊂  LR(1)  ⊂  GLC Não-Ambíguas  ⊂  GLC Gerais                    │
└────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```
