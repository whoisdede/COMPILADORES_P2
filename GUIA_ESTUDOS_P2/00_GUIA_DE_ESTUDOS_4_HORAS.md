# 🎯 GUIA MESTRE DE ESTUDOS DE 4 HORAS – PROVA 2 DE COMPILADORES
**Disciplina:** COM0002 – Compiladores  
**Instituição:** UDESC – Joinville / DCC  
**Professor:** Ricardo Ferreira Martins  
**Foco:** Análise Sintática Top-Down (Descendente) e Bottom-Up (Ascendente)  

---

## ⏱️ O Cronograma Estratégico de 4 Horas (240 Minutos)

Este cronograma foi desenhado para maximizar a sua retenção ativa, cobrindo 100% dos tópicos abordados nas **Listas 02 e 03** e nos slides das **Aulas 03 e 04**. Divida suas 4 horas em 4 blocos de 50 minutos de foco absoluto + 10 minutos de descanso ativo (Pomodoro expandido).

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                     CRONOGRAMA DE 4 HORAS - ANÁLISE SINTÁTICA                 │
├─────────────┬─────────────────────────────────────────────────┬───────────────┤
│ BLOCO       │ CONTEÚDO PRINCIPAL                              │ ARQUIVOS-CHAVE│
├─────────────┼─────────────────────────────────────────────────┼───────────────┤
│ HORA 1      │ Ambiguidade, Árvores, Precedência,              │ 01_...md      │
│ (00m - 50m) │ Fatoração à Esquerda e Recursão à Esquerda     │ 06_...md      │
├─────────────┼─────────────────────────────────────────────────┼───────────────┤
│ HORA 2      │ Algoritmos de FIRST e FOLLOW,                   │ 02_...md      │
│ (60m - 110m)│ Tabela LL(1) Preditiva e Reconhecimento Pilha   │ 06_...md      │
├─────────────┼─────────────────────────────────────────────────┼───────────────┤
│ HORA 3      │ Análise Ascendente LR: Fechamento, Desvio,      │ 03_...md      │
│(120m - 170m)│ Autômatos LR(0)/LR(1), SLR(1), LALR(1) e Confl. │ 04_...md      │
├─────────────┼─────────────────────────────────────────────────┼───────────────┤
│ HORA 4      │ Resolução da Lista 03, Simulado de Prova Real   │ 05_...md      │
│(180m - 230m)│ e Fixação com o Mapa Mental de 1 Folha A4       │ 07_... / 08_..│
└─────────────┴─────────────────────────────────────────────────┴───────────────┘
```

---

## 📋 Detalhamento dos Blocos de Estudo

### 🟢 HORA 1 (0 a 50 min): Gramáticas, Ambiguidade e Transformações Top-Down
- **Meta:** Dominar a semântica das derivações, construir árvores sintáticas respeitando precedência/associatividade e saber transformar qualquer gramática para adequá-la à análise preditiva.
- **Tópicos Obrigatórios:**
  1. Derivações mais à esquerda (*leftmost*) e mais à direita (*rightmost*).
  2. Como operadores determinam a estrutura da árvore (recursão à esquerda = associatividade à esquerda; nós mais profundos = maior precedência).
  3. Identificação e prova de ambiguidade (apresentação de 2 árvores ou 2 derivações distintas para a mesma sentença).
  4. O caso clássico do *Dangling-Else* e armadilhas (análise da Questão 03 da Lista 02).
  5. Algoritmo de Eliminação de Recursão à Esquerda Imediata ($A \to A\alpha \mid \beta$) e Indireta (ordenação de não-terminais).
  6. Algoritmo de Fatoração à Esquerda (*Left Factoring*) para extrair prefixos comuns.
- **Exercícios-Alvo das Listas:** Lista 02 (Q1, Q2, Q3, Q6, Q8).

---

### 🟡 HORA 2 (60 a 110 min): FIRST, FOLLOW e Análise Descendente LL(1)
- **Meta:** Calcular sem errar os conjuntos FIRST de cadeias e FOLLOW de não-terminais por algoritmo de ponto fixo e construir a tabela sintática $M[A, a]$.
- **Tópicos Obrigatórios:**
  1. Definição formal e algoritmo de cálculo do FIRST (de símbolos e de cadeias $X_1 X_2 \dots X_n$).
  2. Definição formal e algoritmo de cálculo do FOLLOW (regras de propagação e inicialização com $\$$ no símbolo inicial).
  3. As duas regras de ouro: FIRST pode conter $\varepsilon$; FOLLOW **nunca** contém $\varepsilon$, mas contém $\$$. FOLLOW só é calculado para não-terminais.
  4. Definição de Gramática LL(1) e condição necessária e suficiente ($FIRST(\alpha) \cap FIRST(\beta) = \emptyset$).
  5. Construção da Tabela Sintática Preditiva $M[A, a]$.
  6. Simulação do algoritmo preditivo não recursivo com Pilha ($X$ vs $a$, desempilha e empilha o reverso).
- **Exercícios-Alvo das Listas:** Lista 02 (Q4, Q5, Q7, Q9).

---

### 🟠 HORA 3 (120 a 170 min): Análise Ascendente LR (LR(0), SLR(1), LR(1), LALR(1))
- **Meta:** Entender a mecânica do *shift-reduce*, construir o AFD de prefixos viáveis através das operações Fechamento e Desvio, e preencher tabelas sintáticas identificando conflitos.
- **Tópicos Obrigatórios:**
  1. Conceito de *handle* (maçaneta) e derivação mais à direita ao contrário.
  2. Itens LR(0) (produção com marcador $\cdot$).
  3. Operações canônicas: $\text{FECHAMENTO}(I)$ e $\text{DESVIO}(I, X)$.
  4. Autômato LR(0) completo e preenchimento da tabela SLR(1).
  5. Onde ocorrem reduções no SLR(1): apenas nos terminais pertencentes a $FOLLOW(A)$!
  6. Conflitos Shift/Reduce (Deslocar/Reduzir) e Reduce/Reduce (Reduzir/Reduzir).
  7. Por que SLR(1) falha no caso do ponteiro/atribuição ($S \to L=R \mid R$) e a solução com LR(1) canônico.
  8. Itens LR(1) $[A \to \alpha \cdot \beta, a]$ e o papel do lookahead propagado.
  9. LALR(1): fusão de estados com o mesmo núcleo (*core*). Consequências da fusão: nunca cria conflito Shift/Reduce, mas pode criar Reduce/Reduce!
- **Exercícios-Alvo das Listas:** Aula 04 (Slides 16 a 33), Lista 03 (Q1, Q2, Q3 - Gramáticas 1 a 6).

---

### 🔴 HORA 4 (180 a 240 min): Simulação de Prova, Lista 03 e Mapa Mental A4
- **Meta:** Treinar velocidade de prova, evitar pegadinhas clássicas e consolidar o mapa mental na memória de trabalho.
- **Atividades:**
  1. Resolver o Simulado de Prova P2 (`07_SIMULADO_PROVA_P2_COM_GABARITO.md`) em tempo real (40 minutos).
  2. Conferir com o gabarito comentado e corrigir pontualmente os erros.
  3. Memorizar a estrutura do Mapa Mental A4 (`08_MAPA_MENTAL_FOLHA_A4.md`), revisando mentalmente cada fórmula e condição de decisão.

---

## 🗂️ Índice dos Arquivos do seu Guia de Estudos

Na pasta `C:\Users\andre\Downloads\COMPILADORES_P2\GUIA_ESTUDOS_P2`, você encontra os seguintes arquivos detalhados:

1. [00_GUIA_DE_ESTUDOS_4_HORAS.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/00_GUIA_DE_ESTUDOS_4_HORAS.md): Este plano de voo e método de estudo.
2. [01_GRAMATICAS_AMBIGUIDADE_E_TRANSFORMACOES.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/01_GRAMATICAS_AMBIGUIDADE_E_TRANSFORMACOES.md): Teoria completa de árvores sintáticas, ambiguidade, eliminação de recursão à esquerda e fatoração, com resolução passo a passo da Lista 02 (Q1, Q2, Q3, Q6, Q8).
3. [02_FIRST_FOLLOW_E_ANALISE_LL1.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/02_FIRST_FOLLOW_E_ANALISE_LL1.md): Teoria e algoritmos tabulares de FIRST/FOLLOW, condição LL(1), construção de tabela e simulação de pilha. Resolução detalhada da Lista 02 (Q4, Q5, Q7, Q9).
4. [03_ANALISE_ASCENDENTE_LR0_SLR1.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/03_ANALISE_ASCENDENTE_LR0_SLR1.md): Análise ascendente LR(0) e SLR(1), Fechamento, Desvio, Autômato, Tabela AÇÃO/DESVIO, rastreamento de pilha e conflitos.
5. [04_ANALISE_LR1_CANONICA_E_LALR1.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/04_ANALISE_LR1_CANONICA_E_LALR1.md): Análise LR(1) canônica com lookahead, LALR(1) por fusão de núcleos, comparação de poder expressivo e hierarquia de gramáticas.
6. [05_RESOLUCAO_COMPLETA_LISTA03.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/05_RESOLUCAO_COMPLETA_LISTA03.md): Resolução exaustiva de todas as 6 gramáticas da Lista 03 (autômatos LR(1)/SLR(1)/LALR(1), tabelas sintáticas, mapeamento de conflitos e propostas de solução sem alterar a linguagem).
7. [06_MANUAL_DE_ALGORITMOS_PASSO_A_PASSO.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/06_MANUAL_DE_ALGORITMOS_PASSO_A_PASSO.md): "Livro de Receitas" direto ao ponto, com o passo a passo algorítmico rigoroso de cada procedimento exigido na prova.
8. [07_SIMULADO_PROVA_P2_COM_GABARITO.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/07_SIMULADO_PROVA_P2_COM_GABARITO.md): Simulado completo idêntico ao modelo de prova do Prof. Ricardo Ferreira Martins, com gabarito pontuado e comentado.
9. [08_MAPA_MENTAL_FOLHA_A4.md](file:///C:/Users/andre/Downloads/COMPILADORES_P2/GUIA_ESTUDOS_P2/08_MAPA_MENTAL_FOLHA_A4.md): Síntese esquemática pura em uma folha A4 mental, **sem nenhum exemplo**, contendo apenas fórmulas, fluxogramas de decisão e regras formais de execução para levar na memória para a prova.

---

## ⚠️ Checkpoints Críticos de Prova (Os 5 erros que mais tiram ponto):
1. **Esquecer o marcador de fim de cadeia $\$$** em $FOLLOW(S)$ e na gramática aumentada ($S' \to S\$$ ou $S' \to S$).
2. **Colocar $\varepsilon$ dentro do conjunto FOLLOW**: $\varepsilon$ NUNCA pertence a um conjunto FOLLOW!
3. **Errar o desempilhamento na redução LR**: ao reduzir pela produção $A \to \beta$, desempilham-se exatamente $2 \times |\beta|$ elementos da pilha (cada símbolo gramatical possui seu estado associado). Se $\beta = \varepsilon$, desempilham-se $0$ elementos!
4. **Confundir a redução do SLR(1) com LR(0)**: No LR(0), o item $[A \to \alpha\cdot]$ reduz em TODAS as colunas de terminais. No SLR(1), ele reduz **apenas** nas colunas dos terminais que pertencem a $FOLLOW(A)$!
5. **Achar que toda gramática não ambígua é LL(1)**: Falso! Gramáticas com recursão à esquerda ou não fatoradas não são ambíguas, mas NÃO são LL(1).
