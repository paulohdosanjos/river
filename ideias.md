# Introdução

A área de planejamento estocástico considera o problema de decisões
sequenciais em ambientes probabilísticos. O resultado de um agente
inteligente é uma política, que dependendo da complexidade do espaço de
estados, não é interpretável por um humano leigo.

Nesse artigo, buscamos estudar o processo geral de explicação do agente
inteligente. Ou seja, as formas nas quais o agente pode explicar ao
usuário do sistema sua decisão. Contribuimos com algoritmos que
facilitam a interpretação de políticas e a argumentação de escolhas.
Usamos o problema abstrato do rio como base para nossas experimentações.

# Background teórico

Consideramos que o problema do agente inteligente pode ser modelado como
um **processo markoviano de decisão** (MDP). Em um processo desse tipo,
há três elementos: estados, ações e custos. O agente começa em um estado
inicial e toma alguma ação com base nesse estado, em seguida, o ambiente
transita para outro estado de acordo com uma distribuição específica, a
qual o agente não tem controle, e o agente recebe um custo numérico. O
processo acaba somente se o ambiente transitar para estados especiais,
chamados estados meta, que como o nome diz, sinaliza a conclusão do
objetivo do agente.

Agora, definimos formalmente um MDP.

Um é definido formalmente como uma tupla $M = (S, A, T, G, C)$. Onde $S$
é o conjunto de estados, $A$ é conjunto de ações possíveis, $T$ é a
matriz de transição que determina as propabilidades de transição do
processo, $G$ é o conjunto de estados objetivo e $C$ é a função custo.

Então, a função custo $C: S \times A \rightarrow \Re$ determina os
custos das transições: $C(s,a)$ fornece o custo quando o agente toma a
ação $a$ no estado $s$. A matriz de transições
$T: S \times A \times S \rightarrow \Re$ determina a distribuição citada
acima: $T(s',a,s')$ é a probabilidade do ambiente transitar para o
estado $s'$ quando a ação $a$ é tomada no estado $s$.

Além disso, introduzimos algumas notações e definições adicionais.

O processo começa no estado $s_0$, então o agente toma a ação $a_0$ de
custo $C(s_0,a_0) = c_0$. O processo então transita para um estado
$s_1$. Em seguida, o agente toma a ação $a_1$ de custo $c_1$. A
sequência de todos esses elementos durante um processo é chamada de
**história**.

## Definições e notação

**Definição 1**. Uma história $h$ de um MDP $M = (S, A , T, G, C)$ é uma
sequência $$h = (s_0,a_0,c_0,s_1,a_1,c_1,\dots)$$

onde $s_i$ é o estado do processo no tempo $i$, $a_i$ é a ação tomada
nesse tempo e $c_i$ é o custo obtido, com $i \in \{0,1,2,...\}$. Ou
seja, uma história é uma possível ocorrência de processo de $M$.

Para nos referirmos a estados, ações e custos dentro de uma história,
definimos a seguir duas notações auxiliares.

**Definição 2**. Seja $h$ a história de um MDP qualquer. Definimos
$s(h,t)$ como o estado em que o processo se encontra no instante $t$.
Analogamente, $a(h,t)$ é ação tomada no instante $t$ e $c(h,t)$ o custo
obtido.

**Definição 3**. Seja $h$ a história de um MDP qualquer. Definimos
$f(h,A)$ como a quantidade de vezes que o estado $A$ aparece na história
$h$.

# Problema do rio

Nessa seção, definimos o problema abstrato que serviu como base para as
experimentações: o problema do rio.

No problema do rio, um agente está em um lado da margem do rio e quer ir
para o outro lado. O agente tem duas opções: (i) nadar de qualquer ponto
da margem do rio, ou (ii) ir ao longo da margem do rio até uma ponte.
Este problema é modelado como uma grade $N_x$ $\times$ $N_y$, onde
$x = 0$ e $x = N_x-1$ representa a margem do rio; e $y = N_y-1$
representa a ponte e $y = 0$ representa uma cachoeira onde o agente pode
ficar preso ou morrer. O objetivo fica do outro lado da margem do rio,
longe do ponte, $x_g = N_x-1$ e $y_g = 0$. O agente pode se mover na
direções cardinais. Se ações são tomadas na margem do rio ou na ponte,
então as transições são determinísticas em relação às respectivas
direções cardeais; se ações são tomadas no rio então as transições são
probabilísticas e seguem a direção cardeal com probabilidade
$1 - P_{river}$ ou segue rio abaixo com probabilidade $P_{river}$ . A
cachoeira é modelada como um estado *dead end*. O custo imediato é $1$.
Veja a imagem abaixo para a visualização do problema.

<figure id="fig:">
<div class="center">
<img src="img/1.png" style="width:95.0%" />
</div>
<figcaption></figcaption>
</figure>

# Intepretabilidade de políticas

A primeira classe de problema é a classe de interpretabilidade de
políticas. Essa classe envolve problemas nos quais o agente fornece
informações para o usuário a fim de resumir o funcionamento de uma
política. Essa seção está estruturada da forma pergunta + resposta, onde
cada pergunta representa um problema que estamos interessados para o
agente. Uma solução é dada para cada pergunta tendo em vista o problema
do rio.

Agora, descrevemos elementos sobre o processo de explicação do agente.
Para isso, devemos definir uma linguagem na qual agente e usuário podem
se comunicar.

Definimos uma **semântica de evento** que servirá como a interface de
consultas de explicações entre agente e usuário. A ideia é que a
semântica de evento defina um formato de eventos que temos interesse no
processo de interpretabilidade de políticas.

Um usuário pode não ter interesse nos detalhes internos de uma política
dada por um agente. Mas tem interesse em um evento de mais alto nível.
Por exemplo, considere um agente inteligente que fornece uma rota da
casa de Bob até o aeroporto. O agente fornece uma política que descreve
como Bob deve agir em cada rua e curva. O agente pode não estar
particularmente interessado, por exemplo, se ele deve virar à direita ou
à esquerda na avenida principal. De certa forma, esse aspecto da
política não gera \"dúvida\" para o usuário e essa explicação não é
necessária. Mas, ele poderia estar interessado se vai passar por um
bairro $x$ ou não. Então, o agente pode dar essa resposta ao usuário,
dando a ele uma compreensão maior do funcionamento da política. Esse é
um exemplo de um problema de **interpretabilidade de políticas**. Nessa
classe de problemas, o agente fornece informações pontuais para o
usuário como forma de resumir a política.

Dado o resultado do agente, uma política, juntamente com o MDP
correspondente, obtém-se uma cadeia de Markov que descreve completamente
o processo. Essa cadeia gera uma distribuição sobre histórias, que por
sua vez gera uma distribuição sobre eventos de interesse. Fornecer a
distribuição completa sobre histórias não é razoável na maioria dos
casos pois exige um esforço cognitivo indefinidamente grande do usuário.
Portanto, buscamos formas alternativas de resumir a política para o
usuário.

Voltando para a semântica de evento. Definimos o conjunto $E$ como o
conjunto de eventos de interesse no processo. A partir desses eventos,
podemos descrever problemas de interpretabilidade e argumentação de
políticas.

## Problema 1

A primeira pergunta é uma simples. Dada uma semântica bem simples para o
conjunto de eventos, queremos saber se um evento acontece. Mais
especificamente:


**Solução.** Introduz estado absorvedor artificíal em A e calcula as
probabilidades limites.

## Problema 2


A solução desse problema permite consideramos fórmulas condicionais
sobre eventos. Por exemplo, se temos interesse em saber se um evento
implica outro, instânciamos o **problema $2$** com
$\phi(\{A,B\}) = \neg A \lor B$, que é equivalente a $A \rightarrow B$.

## Problema 3


Com esse valor, podemos calcular quantas vezes, em média, o processo
passa pelo estado $A$:

$$\mathbb{E}[f(h, A)] = \sum_{i = 0}^{\infty} ie(A,i)$$

Além disso, uma quantidade de interesse é o número de vezes, em média,
que o processo passa pelo estado $A$, dado que passou por ele alguma
vez. Podemos calcular essa quantidade a partir dos valores de $e(A,i)$:
$$\begin{aligned}
  \mathbb{E}[f(h,A) | f(h,A) \ge 1]=& \sum_{i = 0}^{\infty} i \Pr\{f(h,A) = i | f(h,A) \ge 1\} \\
=& \sum_{i = 0}^{\infty} i \frac{\Pr\{f(h,A) = i , f(h,A) \ge 1\}}{\Pr\{f(h,A) \ge 1\}} \\
=& \frac{\sum_{i = 1}^{\infty} i \Pr\{e(A,i)\}}{\sum_{i = 1}^{\infty} \Pr\{e(A,i)\}}
\end{aligned}$$

# Argumentação

Outro aspecto importante do processo de explicação do agente é o de
argumentação. Nesse cenário, o agente é questionado sobre alguma
decisão. No exemplo do Bob, o usuário poderia perguntar para o agente
porque ele escolheu uma política que passa pelo bairro $x$ e não pelo
bairro $y$. A resposta do agente depende do critério de decisão do
processo. Por exemplo, se o objetivo do usuário é uma rota mais curta
possível, o agente pode explicar para o usuário que qualquer rota
passando por $y$ é mais longa que a rota fornecida (como forma de
política).

Para cada pergunta, a resposta pode estar dentro de um de três elementos
do MDP: $G$, $T$ ou $C$. Por exemplo, o usuário pode perguntar: Por que
o evento A acontece? Analisando a partir da função custo, o agente
poderia responder: \"Se não A, então o custo esperado aumenta\". Olhando
para a função objetivo: \"Se não A, a probabilidade de atingir um
objetivo é pequena\". E assim por diante. A partir dessas ideias,
podemos formular perguntas usuário-agente.

## Problema 4


## Problema 5


A área de planejamento estocástico considera o problema de decisões
sequenciais em ambientes probabilísticos. O resultado de um agente
inteligente é uma política, que dependendo da complexidade do espaço de
estados, não é interpretável por um humano leigo.

Nesse artigo, buscamos estudar o processo geral de explicação do agente
inteligente. Ou seja, as formas nas quais o agente pode explicar ao
usuário do sistema sua decisão. Contribuimos com algoritmos que
facilitam a interpretação de políticas e a argumentação de escolhas.
Usamos o problema abstrato do rio como base para nossas experimentações.

# Background teórico

Consideramos que o problema do agente inteligente pode ser modelado como
um **processo markoviano de decisão** (MDP). Em um processo desse tipo,
há três elementos: estados, ações e custos. O agente começa em um estado
inicial e toma alguma ação com base nesse estado, em seguida, o ambiente
transita para outro estado de acordo com uma distribuição específica, a
qual o agente não tem controle, e o agente recebe um custo numérico. O
processo acaba somente se o ambiente transitar para estados especiais,
chamados estados meta, que como o nome diz, sinaliza a conclusão do
objetivo do agente.

Agora, definimos formalmente um MDP.

Um é definido formalmente como uma tupla $M = (S, A, T, G, C)$. Onde $S$
é o conjunto de estados, $A$ é conjunto de ações possíveis, $T$ é a
matriz de transição que determina as propabilidades de transição do
processo, $G$ é o conjunto de estados objetivo e $C$ é a função custo.

Então, a função custo $C: S \times A \rightarrow \Re$ determina os
custos das transições: $C(s,a)$ fornece o custo quando o agente toma a
ação $a$ no estado $s$. A matriz de transições
$T: S \times A \times S \rightarrow \Re$ determina a distribuição citada
acima: $T(s',a,s')$ é a probabilidade do ambiente transitar para o
estado $s'$ quando a ação $a$ é tomada no estado $s$.

Além disso, introduzimos algumas notações e definições adicionais.

O processo começa no estado $s_0$, então o agente toma a ação $a_0$ de
custo $C(s_0,a_0) = c_0$. O processo então transita para um estado
$s_1$. Em seguida, o agente toma a ação $a_1$ de custo $c_1$. A
sequência de todos esses elementos durante um processo é chamada de
**história**.

## Definições e notação

**Definição 1**. Uma história $h$ de um MDP $M = (S, A , T, G, C)$ é uma
sequência $$h = (s_0,a_0,c_0,s_1,a_1,c_1,\dots)$$

onde $s_i$ é o estado do processo no tempo $i$, $a_i$ é a ação tomada
nesse tempo e $c_i$ é o custo obtido, com $i \in \{0,1,2,...\}$. Ou
seja, uma história é uma possível ocorrência de processo de $M$.

Para nos referirmos a estados, ações e custos dentro de uma história,
definimos a seguir duas notações auxiliares.

**Definição 2**. Seja $h$ a história de um MDP qualquer. Definimos
$s(h,t)$ como o estado em que o processo se encontra no instante $t$.
Analogamente, $a(h,t)$ é ação tomada no instante $t$ e $c(h,t)$ o custo
obtido.

**Definição 3**. Seja $h$ a história de um MDP qualquer. Definimos
$f(h,A)$ como a quantidade de vezes que o estado $A$ aparece na história
$h$.

# Problema do rio

Nessa seção, definimos o problema abstrato que serviu como base para as
experimentações: o problema do rio.

No problema do rio, um agente está em um lado da margem do rio e quer ir
para o outro lado. O agente tem duas opções: (i) nadar de qualquer ponto
da margem do rio, ou (ii) ir ao longo da margem do rio até uma ponte.
Este problema é modelado como uma grade $N_x$ $\times$ $N_y$, onde
$x = 0$ e $x = N_x-1$ representa a margem do rio; e $y = N_y-1$
representa a ponte e $y = 0$ representa uma cachoeira onde o agente pode
ficar preso ou morrer. O objetivo fica do outro lado da margem do rio,
longe do ponte, $x_g = N_x-1$ e $y_g = 0$. O agente pode se mover na
direções cardinais. Se ações são tomadas na margem do rio ou na ponte,
então as transições são determinísticas em relação às respectivas
direções cardeais; se ações são tomadas no rio então as transições são
probabilísticas e seguem a direção cardeal com probabilidade
$1 - P_{river}$ ou segue rio abaixo com probabilidade $P_{river}$ . A
cachoeira é modelada como um estado *dead end*. O custo imediato é $1$.
Veja a imagem abaixo para a visualização do problema.

<figure id="fig:">
<div class="center">
<img src="img/1.png" style="width:95.0%" />
</div>
<figcaption></figcaption>
</figure>

# Intepretabilidade de políticas

A primeira classe de problema é a classe de interpretabilidade de
políticas. Essa classe envolve problemas nos quais o agente fornece
informações para o usuário a fim de resumir o funcionamento de uma
política. Essa seção está estruturada da forma pergunta + resposta, onde
cada pergunta representa um problema que estamos interessados para o
agente. Uma solução é dada para cada pergunta tendo em vista o problema
do rio.

Agora, descrevemos elementos sobre o processo de explicação do agente.
Para isso, devemos definir uma linguagem na qual agente e usuário podem
se comunicar.

Definimos uma **semântica de evento** que servirá como a interface de
consultas de explicações entre agente e usuário. A ideia é que a
semântica de evento defina um formato de eventos que temos interesse no
processo de interpretabilidade de políticas.

Um usuário pode não ter interesse nos detalhes internos de uma política
dada por um agente. Mas tem interesse em um evento de mais alto nível.
Por exemplo, considere um agente inteligente que fornece uma rota da
casa de Bob até o aeroporto. O agente fornece uma política que descreve
como Bob deve agir em cada rua e curva. O agente pode não estar
particularmente interessado, por exemplo, se ele deve virar à direita ou
à esquerda na avenida principal. De certa forma, esse aspecto da
política não gera \"dúvida\" para o usuário e essa explicação não é
necessária. Mas, ele poderia estar interessado se vai passar por um
bairro $x$ ou não. Então, o agente pode dar essa resposta ao usuário,
dando a ele uma compreensão maior do funcionamento da política. Esse é
um exemplo de um problema de **interpretabilidade de políticas**. Nessa
classe de problemas, o agente fornece informações pontuais para o
usuário como forma de resumir a política.

Dado o resultado do agente, uma política, juntamente com o MDP
correspondente, obtém-se uma cadeia de Markov que descreve completamente
o processo. Essa cadeia gera uma distribuição sobre histórias, que por
sua vez gera uma distribuição sobre eventos de interesse. Fornecer a
distribuição completa sobre histórias não é razoável na maioria dos
casos pois exige um esforço cognitivo indefinidamente grande do usuário.
Portanto, buscamos formas alternativas de resumir a política para o
usuário.

Voltando para a semântica de evento. Definimos o conjunto $E$ como o
conjunto de eventos de interesse no processo. A partir desses eventos,
podemos descrever problemas de interpretabilidade e argumentação de
políticas.

## Problema 1

A primeira pergunta é uma simples. Dada uma semântica bem simples para o
conjunto de eventos, queremos saber se um evento acontece. Mais
especificamente:

**Solução.** Introduz estado absorvedor artificíal em A e calcula as
probabilidades limites.

## Problema 2


A solução desse problema permite consideramos fórmulas condicionais
sobre eventos. Por exemplo, se temos interesse em saber se um evento
implica outro, instânciamos o **problema $2$** com
$\phi(\{A,B\}) = \neg A \lor B$, que é equivalente a $A \rightarrow B$.

## Problema 3

Com esse valor, podemos calcular quantas vezes, em média, o processo
passa pelo estado $A$:

$$\mathbb{E}[f(h, A)] = \sum_{i = 0}^{\infty} ie(A,i)$$

Além disso, uma quantidade de interesse é o número de vezes, em média,
que o processo passa pelo estado $A$, dado que passou por ele alguma
vez. Podemos calcular essa quantidade a partir dos valores de $e(A,i)$:
$$\begin{aligned}
  \mathbb{E}[f(h,A) | f(h,A) \ge 1]=& \sum_{i = 0}^{\infty} i \Pr\{f(h,A) = i | f(h,A) \ge 1\} \\
=& \sum_{i = 0}^{\infty} i \frac{\Pr\{f(h,A) = i , f(h,A) \ge 1\}}{\Pr\{f(h,A) \ge 1\}} \\
=& \frac{\sum_{i = 1}^{\infty} i \Pr\{e(A,i)\}}{\sum_{i = 1}^{\infty} \Pr\{e(A,i)\}}
\end{aligned}$$

# Argumentação

Outro aspecto importante do processo de explicação do agente é o de
argumentação. Nesse cenário, o agente é questionado sobre alguma
decisão. No exemplo do Bob, o usuário poderia perguntar para o agente
porque ele escolheu uma política que passa pelo bairro $x$ e não pelo
bairro $y$. A resposta do agente depende do critério de decisão do
processo. Por exemplo, se o objetivo do usuário é uma rota mais curta
possível, o agente pode explicar para o usuário que qualquer rota
passando por $y$ é mais longa que a rota fornecida (como forma de
política).

Para cada pergunta, a resposta pode estar dentro de um de três elementos
do MDP: $G$, $T$ ou $C$. Por exemplo, o usuário pode perguntar: Por que
o evento A acontece? Analisando a partir da função custo, o agente
poderia responder: \"Se não A, então o custo esperado aumenta\". Olhando
para a função objetivo: \"Se não A, a probabilidade de atingir um
objetivo é pequena\". E assim por diante. A partir dessas ideias,
podemos formular perguntas usuário-agente.

## Problema 4

## Problema 5
