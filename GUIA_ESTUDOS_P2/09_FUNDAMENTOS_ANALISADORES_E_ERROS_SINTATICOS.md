# MÓDULO 09: FUNDAMENTOS DA COMPILAÇÃO – OS 3 ANALISADORES E ERROS SINTÁTICOS

---

## 1. O Analisador Sintático: Conceito, Definição Formal e Papel no Compilador

### 1.1 O que é o Analisador Sintático?
O **Analisador Sintático** (comumente denominado ***Parser***) é o componente central da fase de análise (*front-end*) de um compilador. Sua função primordial é obter uma sequência linear de tokens produzida pelo analisador léxico e verificar se essa sequência pode ser legitimamente gerada pelas regras estruturais da linguagem de programação.

```
                  ┌──────────────────────┐
                  │    Código-Fonte      │
                  └──────────┬───────────┘
                             │ Fluxo de Caracteres
                             ▼
                  ┌──────────────────────┐
                  │  1. Analisador Léxico│
                  └──────────┬───────────┘
                             │ Fluxo de Tokens (tipo, valor)
                             ▼
                  ┌──────────────────────┐ ◄─── Consulta Gramática Livre de Contexto (GLC)
                  │2. Analisador Sintático│
                  └──────────┬───────────┘
                             │ Árvore Sintática (AST / Parse Tree)
                             ▼
                  ┌──────────────────────┐
                  │3. Analisador Semântico│
                  └──────────────────────┘
```

### 1.2 Definição Formal
Formalmente, o analisador sintático é um sistema reconhecedor baseado em um **Autômato com Pilha** que decide o problema da pertinência de uma cadeia $w$ em relação a uma **Gramática Livre de Contexto (GLC)**:
$$w \in L(G) \quad ?$$
Onde:
- $G = (V, T, P, S)$ é a gramática formal que define a sintaxe da linguagem.
- $w = a_1 a_2 \dots a_n \$$ é a sequência de terminais (tokens) recebida do analisador léxico.
- Se $S \Rightarrow^* w$, o analisador aceita a sentença e gera como produto a sua **Árvore de Derivação** (ou executa ações semânticas correspondentes).
- Se $S \not\Rightarrow^* w$, o analisador rejeita a cadeia, isola a falha e dispara rotinas de **tratamento e recuperação de erro sintático**.

### 1.3 Responsabilidades Principais do Parser:
1. **Verificação de Validade Estrutural:** Garantir que comandos, expressões, declarações e blocos sigam estritamente a ordem prescrita pela gramática da linguagem.
2. **Construção da Representação Hierárquica:** Converter a sequência plana de tokens em uma **Árvore Sintática Concreta** (*Parse Tree*) ou **Árvore Sintática Abstrata** (*AST*).
3. **Detecção e Diagnóstico de Erros:** Identificar com precisão onde a estrutura foi violada, informando a linha, a coluna e os tokens esperados vs. recebidos.
4. **Recuperação de Falhas:** Permitir que a compilação prossiga após encontrar um erro, para reportar o máximo de problemas em uma única execução sem entrar em loop infinito.

---

## 2. Os 3 Analisadores do Compilador (*Front-End*)

O processo de compilação divide-se em *Front-End* (análise do código-fonte) e *Back-End* (síntese e geração de código de máquina). O *front-end* é composto por três analisadores sequenciais e complementares:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                              FRONT-END DE UM COMPILADOR                                │
├───────────────────────┬───────────────────────────────┬────────────────────────────────┤
│ 1. ANALISADOR LÉXICO  │ 2. ANALISADOR SINTÁTICO       │ 3. ANALISADOR SEMÂNTICO        │
│    (Scanner / Lexer)  │    (Parser)                   │    (Type & Context Checker)    │
├───────────────────────┼───────────────────────────────┼────────────────────────────────┤
│ "Reconhece Palavras"  │ "Reconhece Frases/Estruturas" │ "Verifica o Sentido e Tipos"   │
└───────────────────────┴───────────────────────────────┴────────────────────────────────┘
```

---

### 2.1 Analisador Léxico (Scanner / Lexer)
- **Função:** É o primeiro contato com o código-fonte. Lê o arquivo caractere por caractere, agrupa esses caracteres em padrões significativos chamados **lexemas** e converte-os em unidades atômicas tipadas denominadas **tokens**.
- **Tarefas Adicionais:**
  - Descarta espaços em branco (*whitespaces*), tabulações e quebras de linha que não tenham valor sintático.
  - Remove comentários do código.
  - Mantém o contador de linhas e colunas para rastrear a origem dos erros.
  - Insere identificadores e literais na **Tabela de Símbolos**.
- **Fundamento Teórico e Formalismo:**
  - **Linguagens Regulares (Tipo 3 da Hierarquia de Chomsky).**
  - **Expressões Regulares (ER)** para especificação dos padrões de tokens.
  - **Autômatos Finitos Determinísticos (AFD)** e **Não-Determinísticos (AFN)** para o reconhecimento em tempo linear $O(n)$.
- **Ferramentas Típicas:** Lex, Flex, JFlex.

---

### 2.2 Analisador Sintático (Parser)
- **Função:** Recebe os tokens fornecidos pelo analisador léxico e valida se a ordem e o agrupamento desses tokens formam sentenças e estruturas válidas de acordo com as regras de produção da linguagem.
- **Diferencial com o Léxico:** Enquanto o léxico não possui memória de aninhamento (não consegue saber se parênteses estão balanceados ou se um bloco `if` foi fechado por `end`), o analisador sintático utiliza uma **Pilha** para tratar hierarquias e aninhamentos arbitrários.
- **Fundamento Teórico e Formalismo:**
  - **Linguagens Livres de Contexto (Tipo 2 da Hierarquia de Chomsky).**
  - **Gramáticas Livres de Contexto (GLC) / Notação BNF**.
  - **Autômatos com Pilha (Pushdown Automata)**.
  - Algoritmos Determinísticos:
    - *Top-Down:* Descendente Recursivo, LL(1).
    - *Bottom-Up:* Precedência de Operadores, LR(0), SLR(1), LALR(1), LR(1).
- **Ferramentas Típicas:** Yacc, Bison, ANTLR, JavaCC.

---

### 2.3 Analisador Semântico (Semantic Analyzer / Verificador de Tipos)
- **Função:** Garante que o programa atenda às restrições lógicas e sensíveis ao contexto da linguagem que **não podem** ser expressas por uma Gramática Livre de Contexto.
- **Tarefas Principais:**
  - **Verificação de Tipos (*Type Checking*):** Verifica se operadores são aplicados a operandos compatíveis (ex: proibir somar um ponteiro com uma `struct`).
  - **Resolução de Escopo e Declaração:** Garante que toda variável usada foi previamente declarada no escopo visível e não foi declarada duplamente no mesmo bloco.
  - **Checagem de Funções:** Valida se a quantidade e os tipos dos argumentos passados em uma chamada de função coincidem com a sua assinatura no protótipo.
  - **Verificação de Controle de Fluxo:** Verifica se comandos como `break` ou `continue` ocorrem estritamente dentro de laços (`while`, `for`) e se funções com retorno de fato retornam valor em todos os caminhos de execução.
- **Fundamento Teórico e Formalismo:**
  - **Definições Dirigidas pela Sintaxe (DDS)** e **Esquemas de Tradução Dirigida pela Sintaxe (TDS)**.
  - **Atributos Sintetizados** (avaliados de baixo para cima) e **Atributos Herdados** (avaliados de cima para baixo / entre irmãos).
  - Definições **S-atribuídas** (apenas atributos sintetizados) e **L-atribuídas** (atributos herdam apenas da esquerda).
- **Saída:** Árvore Sintática Decorada / Anotada e Tabela de Símbolos completamente validada, pronta para a geração de código intermediário (ex: código de 3 endereços).

---

### 2.4 Matriz Comparativa Definitiva dos 3 Analisadores

| Critério | Analisador Léxico | Analisador Sintático | Analisador Semântico |
| :--- | :--- | :--- | :--- |
| **Objeto de Análise** | Caracteres individuais | Sequência de Tokens | Árvore Sintática e Significados |
| **Modelo Matemático** | Expressões Regulares / Autômato Finito | GLC / Autômato com Pilha | Gramática de Atributos / DDS |
| **Entrada** | Código-fonte puro (texto) | Sequência de Tokens | Árvore Sintática (AST) |
| **Saída** | Tokens `(tipo, valor)` | Árvore Sintática (Parse Tree / AST) | AST Decorada / Código Intermediário |
| **Hierarquia Chomsky**| Tipo 3 (Linguagens Regulares) | Tipo 2 (Livres de Contexto) | Tipo 1 / Tipo 0 (Sensíveis ao Contexto) |
| **Usa Pilha?** | ❌ Não (Sem memória de pilha) | ✅ Sim (Pilha de parsing) | ✅ Sim (Pilha semântica / Tabelas) |

---

## 3. O que é um Erro Sintático? Definição e Exemplos Práticos

### 3.1 Definição Formal de Erro Sintático
Um **Erro Sintático** ocorre quando o fluxo de tokens fornecido pelo analisador léxico viola as regras de produção da Gramática Livre de Contexto que rege a linguagem. 
Em termos de teoria da computação:
> "Ocorre um erro sintático no instante em que a cadeia parcial de tokens já processada $w$ combinada com o próximo token de entrada $a$ torna impossível a existência de qualquer derivação válida que leve a uma sentença legal da linguagem a partir do símbolo inicial ($S \not\Rightarrow^* w a \gamma$)."

### 3.2 A Fronteira Crítica: Léxico vs. Sintático vs. Semântico

Para nunca mais errar essa distinção em provas e entrevistas:

```
Código de Exemplo:       resultado = valor + @;
                         ▲                   ▲
                         │                   └─ ERRO LÉXICO: O caractere '@' não faz parte 
                         │                      do alfabeto de tokens da linguagem!
                         └─ O analisador nem chega a avaliar sintaxe se o léxico travar aqui.

Código de Exemplo:       int x = 10 + * 5;
                                      ▲
                                      └─ ERRO SINTÁTICO: Todos os tokens existem individualmente 
                                         ('int', 'x', '=', '10', '+', '*', '5', ';'), mas a 
                                         ordem "+ *" viola a regra "EXP -> EXP + TERMO".

Código de Exemplo:       int x = "texto" * 3.14;
                                         ▲
                                         └─ ERRO SEMÂNTICO: A sintaxe é perfeita (Tipo id = EXP * EXP;),
                                            mas a operação de multiplicação entre String e Float é 
                                            semanticamente ilegal na tipagem estática.
```

---

### 3.3 Exemplos Reais de Erros Sintáticos e Diagnóstico pelo Parser

#### Exemplo 1: Operadores Binários Consecutivos ou Faltando Operando
- **Código:**
  ```c
  float total = preco * + 1.10;
  ```
- **Visão do Parser:**
  - O analisador espera a regra `T -> T * F`.
  - Ao encontrar `*`, a regra exige que o próximo símbolo inicie um fator `F` (que pela gramática começa com `(`, `id` ou `num`).
  - Em vez disso, o lookahead encontra o token de adição `+`.
  - **Diagnóstico:** *Syntax Error: unexpected operator '+' after operator '*' at line 1.*

---

#### Exemplo 2: Delimitadores Desbalanceados ou Ausência de Fechamento
- **Código:**
  ```c
  if (x > 0 {
      total = x;
  }
  ```
- **Visão do Parser:**
  - A produção gramatical é:
    $$\text{CMD} \to \text{if} \; ( \; \text{EXP} \; ) \; \text{BLOCO}$$
  - O analisador consome `if`, consome `(`, consome a expressão `x > 0`.
  - O estado atual exige estritamente o token terminal `)`.
  - O próximo token da entrada é `{`. Como `{` não é `)`, ocorre falha imediata na tabela de parsing.
  - **Diagnóstico:** *Syntax Error: expected ')' before '{' at line 1.*

---

#### Exemplo 3: Estrutura de Repetição com Formato Ilegal
- **Código:**
  ```c
  while do x > 10 {
      x--;
  }
  ```
- **Visão do Parser:**
  - A gramática possui produções distintas:
    - $\text{CMD} \to \text{while} \; ( \; \text{EXP} \; ) \; \text{CMD}$
    - $\text{CMD} \to \text{do} \; \text{CMD} \; \text{while} \; ( \; \text{EXP} \; ) \; ;$
  - Ao ler o token `while`, a gramática exige `(`. O encontro do token `do` gera uma célula vazia na tabela $M[\text{CMD}, do]$ ou no estado do autômato LR.
  - **Diagnóstico:** *Syntax Error: unexpected token 'do' after 'while' at line 1.*

---

#### Exemplo 4: Tokens em Ordem Reversa ou Invertida
- **Código:**
  ```c
  int 10 x;
  ```
- **Visão do Parser:**
  - A regra é $\text{DECL} \to \text{TIPO} \; id \; ;$.
  - O parser lê `int` (TIPO). Em seguida, exige que o próximo token seja um identificador ($id$).
  - O token lido é uma constante numérica (`10`).
  - **Diagnóstico:** *Syntax Error: expected identifier after type specifier, got '10' at line 1.*

---

### 3.4 Como o Analisador Sintático Trata e Recupera Erros

Quando um erro sintático é detectado, o compilador **não pode simplesmente abortar**. Ele deve emitir a mensagem e tentar recuperar o sincronismo para achar outros possíveis erros no restante do arquivo.

Existem 4 métodos clássicos de recuperação (vistos nos slides da Aula 03 e no Livro do Dragão):

1. **Modo Pânico (*Panic Mode*):**
   - É a estratégia mais simples e popular. Ao encontrar um erro, o parser descarta os tokens da entrada um a um até encontrar um token de um conjunto pré-definido de **tokens de sincronismo** (geralmente `;`, `}` ou `end`).
   - *Vantagem:* Simplicidade extrema e garantia absoluta de não entrar em loop infinito.
   - *Papel do FOLLOW:* Conforme visto no slide e em `firstfollow.txt`, os conjuntos **FOLLOW** do não-terminal que falhou são tipicamente usados como tokens de sincronismo!

2. **Recuperação em Nível de Frase (*Phrase-Level Recovery*):**
   - O parser realiza uma alteração cirúrgica no restante da entrada: insere um token ausente (ex: insere um `;` virtual), exclui um token bizarro ou substitui uma vírgula por ponto-e-vírgula.
   - *Risco:* Se a heurística falhar, pode disparar uma cascata de falsos erros subsequentes.

3. **Produções de Erro (*Error Productions*):**
   - O autor da linguagem estende a própria gramática incluindo produções que capturam erros comuns dos programadores (ex: `CMD -> if EXP then CMD` para linguagens que exigem parênteses).
   - O parser reconhece o erro como uma produção válida especial e emite um aviso pedagógico sem quebrar o fluxo.

4. **Correção Global (*Global Correction*):**
   - Algoritmo que calcula matematicamente a menor quantidade de inserções, substituições e exclusões para transformar a cadeia com erro em uma sentença gramaticalmente perfeita.
   - *Desvantagem:* Custo computacional excessivo ($O(n^3)$), impraticável em compiladores comerciais.

---

## 4. Bateria de Perguntas Variadas com Gabarito Comentado

Teste seus conhecimentos com estas 10 questões de níveis conceituais e práticos.

---

### Questão 01 (Múltipla Escolha – Papel do Parser)
Qual das seguintes tarefas **NÃO** é de responsabilidade do analisador sintático de um compilador?
- **A)** Verificar se toda instrução `while` possui uma expressão de teste válida.
- **B)** Determinar se os tipos de uma atribuição como `x = "texto"` são compatíveis com a variável `int x`.
- **C)** Construir a árvore sintática concreta ou abstrata do programa.
- **D)** Detectar a ausência de um parêntese de fechamento em uma expressão.
- **E)** Reportar que um bloco de comando não foi encerrado por uma chave `}`.

---

### Questão 02 (Múltipla Escolha – Classificação de Falhas)
Considere o seguinte trecho em C:
```c
int soma(int a, int b) {
    return a + b;
}
int main() {
    int res = soma(10);
    return 0;
}
```
A chamada `soma(10)` viola a definição da função. Que tipo de erro é esse e qual analisador tem a responsabilidade de detectá-lo?
- **A)** Erro Léxico, detectado pelo Analisador Léxico.
- **B)** Erro Sintático, detectado pelo Analisador Sintático.
- **C)** Erro Semântico, detectado pelo Analisador Semântico.
- **D)** Erro de Execução (Runtime), detectado apenas pelo Sistema Operacional.
- **E)** Erro Sintático no analisador ascendente SLR(1).

---

### Questão 03 (Discursiva – Poder Computacional)
Por que um Analisador Léxico (baseado em Expressões Regulares e Autômatos Finitos) é incapaz de verificar se uma estrutura de parênteses aninhados como `( ( ( a + b ) * c ) )` está devidamente balanceada, exigindo a intervenção do Analisador Sintático?

---

### Questão 04 (Identificação de Erros)
Para cada uma das linhas de código abaixo, aponte a linha onde o compilador falhará e classifique o erro como **LÉXICO**, **SINTÁTICO** ou **SEMÂNTICO**, justificando tecnicamente sua resposta:

1. `int #contador = 0;`
2. `if (a > 10) then x = 1;` *(em linguagem C padrão)*
3. `const int PI = 3.14; PI = 3.1415;`
4. `float y = (x + 2 * ;`

---

### Questão 05 (Discursiva – Recuperação em Modo Pânico)
No contexto de análise sintática descendente e ascendente, explique o que é o **Modo Pânico** de recuperação de erros. Como os conjuntos matemáticos **FOLLOW** são utilizados como suporte nessa técnica?

---

### Questão 06 (Múltipla Escolha – Hierarquia dos Analisadores)
Sobre a relação entre os três analisadores do compilador, assinale a afirmação correta:
- **A)** O analisador léxico envia caracteres diretamente para o analisador semântico.
- **B)** Se um programa não tiver nenhum erro sintático, é garantido que ele não terá nenhum erro semântico.
- **C)** O analisador sintático opera sobre tokens, sendo totalmente indiferente aos caracteres individuais que formaram cada lexema.
- **D)** O analisador semântico substitui completamente a necessidade do analisador sintático em compiladores modernos.
- **E)** Um erro léxico sempre é classificado como um caso particular de erro sintático.

---

### Questão 07 (Discursiva – Gramática de Atributos)
O analisador sintático constrói a árvore, mas o analisador semântico a decora. O que são **atributos sintetizados** e **atributos herdados** em uma Definição Dirigida pela Sintaxe (DDS)?

---

### Questão 08 (Discursiva – Erros em Analisadores Ascendentes LR)
Em qual momento exato do algoritmo do analisador sintático ascendente LR (com pilha de estados e tabela de AÇÃO) é detectado um erro sintático? Como a tabela de AÇÃO reflete essa condição?

---

### Questão 09 (Múltipla Escolha – Ambiguidade e Erro)
Uma gramática ambígua:
- **A)** Sempre aceita programas que possuem erros de sintaxe.
- **B)** Impede que um analisador sintático determinístico (como LL ou LR) tome decisões unívocas, gerando conflitos nas tabelas.
- **C)** É necessária para compilar qualquer linguagem orientada a objetos.
- **D)** Converte automaticamente erros semânticos em erros sintáticos.
- **E)** Só pode ser reconhecida por analisadores léxicos baseados em AFN.

---

### Questão 10 (Discursiva – Diferença entre Árvore Concreta e AST)
Qual a diferença fundamental entre a **Árvore Sintática Concreta** (*Parse Tree*) gerada pelo analisador sintático e a **Árvore Sintática Abstrata** (*AST*) repassada para o analisador semântico e fases posteriores?

---
---

# 🔑 GABARITO OFICIAL COMENTADO DAS PERGUNTAS

### Resposta da Questão 01: Alternativa B
- **Justificativa:** A verificação de compatibilidade de tipos entre uma variável inteira e um literal de texto (`x = "texto"`) é uma restrição sensível ao contexto, de competência estrita do **Analisador Semântico**. O parser apenas verifica que a estrutura é `VARIAVEL = EXPRESSAO ;`, o que é sintaticamente impecável. Todas as outras alternativas (A, C, D, E) são responsabilidades diretas do analisador sintático.

---

### Resposta da Questão 02: Alternativa C
- **Justificativa:** A chamada `soma(10)` possui sintaxe perfeita: um identificador seguido de parêntese, expressão e parêntese fechado (`CHAMADA -> id ( ARG )`). O parser aceita a construção. Quem detecta que a função `soma` foi declarada com **dois parâmetros inteiros** e está recebendo apenas **um argumento** é o **Analisador Semântico**, consultando a assinatura na Tabela de Símbolos.

---

### Resposta da Questão 03
- **Resposta:** Expressões Regulares e Autômatos Finitos (modelos do analisador léxico) possuem uma quantidade finita de estados e **não dispõem de memória auxiliar (pilha)**. O problema dos parênteses balanceados (conhecido na teoria da computação como a Linguagem de Dyck, $L = \{ (^n )^n \mid n \ge 1 \}$) exige contagem ou empilhamento arbitrário de aberturas para casar com os fechamentos. Pelo Lema do Bombeamento para Linguagens Regulares, essa linguagem não é regular. Portanto, somente um modelo com pilha (Gramáticas Livres de Contexto e Autômatos com Pilha, usados no analisador sintático) tem poder computacional suficiente para validar o aninhamento.

---

### Resposta da Questão 04
1. `int #contador = 0;` $\implies$ **ERRO LÉXICO**. O caractere `#` não é reconhecido como parte válida de um identificador em linguagens derivadas de C (a menos de diretivas de pré-processador no início da linha). O scanner falha em produzir o token.
2. `if (a > 10) then x = 1;` $\implies$ **ERRO SINTÁTICO**. Em C, a gramática do comando `if` não possui a palavra-chave `then`. O parser espera um comando imediatamente após `)`, mas encontra o token `then`.
3. `const int PI = 3.14; PI = 3.1415;` $\implies$ **ERRO SEMÂNTICO**. A estrutura de atribuição é sintaticamente legal, mas viola a invariante semântica de imutabilidade de constantes registradas na Tabela de Símbolos.
4. `float y = (x + 2 * ;` $\implies$ **ERRO SINTÁTICO**. O operador binário de multiplicação `*` exige um fator à sua direita (`T -> T * F`). O encontro de `;` viola diretamente as regras gramaticais de expressão.

---

### Resposta da Questão 05
- **Resposta:** O **Modo Pânico** é uma técnica de recuperação onde o parser, ao encontrar uma célula de erro (vazia na tabela), entra em estado de descarte sistemático de tokens da entrada até reencontrar um token de sincronismo estável.  
  Os conjuntos **FOLLOW** são ideais como tokens de sincronismo porque representam exatamente os símbolos terminais que podem suceder legitimamente a construção sintática que falhou. Ao descartar a cadeia até encontrar um elemento de $\text{FOLLOW}(A)$, o parser pode simplesmente assumir que o não-terminal $A$ terminou ou foi reduzido/desempilhado, retomando a análise normal no símbolo seguinte sem travar.

---

### Resposta da Questão 06: Alternativa C
- **Justificativa:** O analisador sintático trabalha em um nível de abstração superior: ele opera sobre a sequência de tipos de tokens fornecidos pelo léxico (ex: `T_ID`, `T_SOMA`, `T_NUM`). Se o identificador era `x` ou `variavelDeNomeSuperLongo123`, para a estrutura sintática é irrelevante; o parser enxerga apenas o token terminal `id`.

---

### Resposta da Questão 07
- **Resposta:**
  - **Atributos Sintetizados:** São atributos semânticos de um nó da árvore cujo valor é calculado a partir dos valores dos atributos dos seus nós **filhos** (computados de baixo para cima na árvore). Exemplo: o valor de uma expressão $E \to E_1 + T$ é dado por $E.val = E_1.val + T.val$.
  - **Atributos Herdados:** São atributos cujo valor é calculado a partir dos atributos do nó **pai** ou de nós **irmãos** adjacentes (computados de cima para baixo ou da esquerda para a direita). Exemplo clássico: repassar o tipo de dado em uma declaração $D \to T \; L$, onde a lista de variáveis $L$ herda o tipo computado em $T$ ($L.tipo = T.tipo$).

---

### Resposta da Questão 08
- **Resposta:** No analisador LR, o erro sintático é detectado no momento exato em que o estado atual $s$ no topo da pilha, confrontado com o token corrente $a$ da entrada, consulta a tabela de ação e encontra a entrada vazia:
  $$\text{AÇÃO}[s, a] = \text{VAZIO / INDEFINIDO}$$
  Isso significa que nenhum deslocamento (*shift*) é viável com o terminal $a$ e nenhuma produção é elegível para redução (*reduce*), atestando que a sentença não pode pertencer à linguagem.

---

### Resposta da Questão 09: Alternativa B
- **Justificativa:** A ambiguidade significa que uma mesma sentença possui mais de uma árvore sintática válida. Em algoritmos determinísticos (LL e LR), isso gera **conflitos de escolha**: no LL(1) gera duas regras na mesma célula $M[A, a]$; no LR gera conflitos *Shift/Reduce* ou *Reduce/Reduce*. Como o analisador precisa ser determinístico e ter tempo linear $O(n)$, ele não consegue decidir qual caminho seguir sem regras adicionais de desambiguação.

---

### Resposta da Questão 10
- **Resposta:**
  - A **Árvore Sintática Concreta (*Parse Tree*):** Contém absolutamente todos os nós intermediários, pontuações, parênteses e tokens da gramática, refletindo exatamente cada passo da derivação formal. É prolixa e redundante para as fases posteriores.
  - A **Árvore Sintática Abstrata (*AST*):** É uma versão limpa e condensada. Os nós internos representam operadores ou instruções de controle (ex: `+`, `if`), e os nós folhas representam os operandos (variáveis e constantes). Todos os delimitadores meramente sintáticos (como parênteses, ponto-e-vírgula e chaves) são descartados, pois a própria hierarquia dos nós da árvore já expressa a ordem correta de execução.
