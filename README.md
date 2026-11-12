# 📚 Compiladores P2 – UDESC Joinville (COM0002)
**Professor:** Ricardo Ferreira Martins  
**Foco:** Análise Sintática Top-Down (Descendente) e Bottom-Up (Ascendente)  
**Aluno:** André Vinícius Trentini  

---

## ⚡ Simulador Interativo de Algoritmos (Offline)
Abra diretamente no navegador o arquivo [`simulador.html`](simulador.html) para visualizar graficamente e interagir com:
- **FIRST & FOLLOW:** cálculo iterativo de ponto fixo passo a passo.
- **Transformações:** eliminação de recursão à esquerda e fatoração automática.
- **Analisador LL(1):** tabela preditiva $M[A, a]$ e simulação animada com pilha.
- **Autômato LR(0) / SLR(1):** coleção canônica de itens e diagnóstico de conflitos.
- **Analisador Ascendente LR:** rastreamento de pilha (shift-reduce) em tempo real.
- **LR(1) vs LALR(1):** visualização da fusão de núcleos (*cores*).
- **Presets Integrados:** inclui todas as gramáticas da Aula 03, Aula 04, Lista 02 e Lista 03!

---

## 📖 Módulos do Guia de Estudos (Pasta `GUIA_ESTUDOS_P2/`)

1. [**00_GUIA_DE_ESTUDOS_4_HORAS.md**](GUIA_ESTUDOS_P2/00_GUIA_DE_ESTUDOS_4_HORAS.md): Cronograma mestre de 4 horas (4 blocos de 50 minutos) e os 5 erros mais comuns que tiram nota na prova.
2. [**01_GRAMATICAS_AMBIGUIDADE_E_TRANSFORMACOES.md**](GUIA_ESTUDOS_P2/01_GRAMATICAS_AMBIGUIDADE_E_TRANSFORMACOES.md): Árvores sintáticas, precedência/associatividade, eliminação de recursão à esquerda e fatoração. **Resolução completa da Lista 02 (Q1, Q2, Q3, Q6 e Q8)** com contraexemplo formal para o *dangling-else*.
3. [**02_FIRST_FOLLOW_E_ANALISE_LL1.md**](GUIA_ESTUDOS_P2/02_FIRST_FOLLOW_E_ANALISE_LL1.md): Algoritmos tabulares de FIRST e FOLLOW, condições LL(1), montagem da tabela $M[A, a]$ e simulação de pilha. **Resolução completa da Lista 02 (Q4, Q5, Q7 e Q9)**.
4. [**03_ANALISE_ASCENDENTE_LR0_SLR1.md**](GUIA_ESTUDOS_P2/03_ANALISE_ASCENDENTE_LR0_SLR1.md): Itens LR(0), Fechamento, Desvio, Autômato, tabela SLR(1), simulação de pilha ($2 \times |\beta|$) e estudo do caso clássico do conflito $L=R \mid R$.
5. [**04_ANALISE_LR1_CANONICA_E_LALR1.md**](GUIA_ESTUDOS_P2/04_ANALISE_LR1_CANONICA_E_LALR1.md): Itens com lookahead $[A \to \alpha \cdot \beta, a]$, cálculo de $\text{FIRST}(\beta a)$, fusão de núcleos em LALR(1) e teoremas de conflitos (Shift/Reduce vs Reduce/Reduce).
6. [**05_RESOLUCAO_COMPLETA_LISTA03.md**](GUIA_ESTUDOS_P2/05_RESOLUCAO_COMPLETA_LISTA03.md): **Resolução integral de todas as 6 gramáticas da Lista 03 (Q1, Q2 e Q3)**: autômatos, tabelas, identificação de conflitos e propostas de solução sem alterar a linguagem.
7. [**06_MANUAL_DE_ALGORITMOS_PASSO_A_PASSO.md**](GUIA_ESTUDOS_P2/06_MANUAL_DE_ALGORITMOS_PASSO_A_PASSO.md): "Livro de Receitas" direto ao ponto: guia algorítmico passo a passo para executar manualmente qualquer algoritmo na prova.
8. [**07_SIMULADO_PROVA_P2_COM_GABARITO.md**](GUIA_ESTUDOS_P2/07_SIMULADO_PROVA_P2_COM_GABARITO.md): Simulado de prova de 100 pontos idêntico ao modelo da UDESC, com gabarito pontuado e comentado.
9. [**08_MAPA_MENTAL_FOLHA_A4.md**](GUIA_ESTUDOS_P2/08_MAPA_MENTAL_FOLHA_A4.md): **Síntese mental pura em 1 folha A4**: contém estritamente regras, fórmulas, esquemas e critérios de decisão **sem nenhum exemplo**, pronta para memorização antes da prova.
10. [**09_FUNDAMENTOS_ANALISADORES_E_ERROS_SINTATICOS.md**](GUIA_ESTUDOS_P2/09_FUNDAMENTOS_ANALISADORES_E_ERROS_SINTATICOS.md): Teoria completa do Parser, comparação dos 3 analisadores (Léxico, Sintático e Semântico), definição de erros sintáticos e bateria de 10 questões comentadas.
