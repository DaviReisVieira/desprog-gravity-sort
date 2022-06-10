# Bead Sort

## Introdução

Existem diversos tipos de algoritmos de comparação. Ente eles temos:

- Selection sorts: Selection sort, Heapsort, Smoothsort...

- Insertion sorts: Insertion sort Shell sort Splay sort...

- Distribution sorts: American flag sort, Bead sort, Bucket sort...

De modo geral, algoritmos de distribuição repartem os dados em diferentes espaços de memória (buckets), que são usados, posteriormente, para compor o output.

Para iniciar a explicação do Bead Sort, precisamos pensar em um ábaco.

![](abaco.svg)

??? Checkpoint 1

Quais valores podemos contar no ábaco?

::: Resposta
Apenas números positivos e inteiros. As restrições do ábaco também são aplicáveis ao Bead sort.
:::

???

??? Checkpoint 2

Com as nossas condições montadas, como podemos organizar nossas linhas em ordem crescente?

!!! Dica
Pense na mudança dos eixos.
!!!

::: Resposta
Se transformarmos os eixos horizontais em verticais, por força da gravidade as peças irão cair, preenchendo os espaços vazios. Daí vem o nome pelo qual o algoritmo é mais conhecido: **Gravity Sort**.
:::

???

!!! Mão na massa
Aqui temos uma [ferramenta interativa](https://davireisvieira.github.io/desprog-gravity-sort-site/) que vai facilitar o entendimento de como a ideia de mudar os eixos do ábaco funciona. Essa parte é fundamental para você concretizar o que viu até agora.

!!!

Com a analogia feita, podemos partir para a implementação do algoritmo.

## Implementação em matriz

Mas pera... Um ábaco é um objeto físico. O que pode ser análogo a ele em termos de programação?

:abaco_matriz

??? Atividade

Pense em algo que possamos utilizar para `md substituir` o ábaco na hora de programar.

::: Gabarito
Imagine que cada bolinha do ábaco é um elemento e cada haste é um vetor. Exato! Um ábaco pode ser visto como uma matriz de `md TRUE` e `md FALSE`, ou `md 1s` e `md 0s`.

![](abaco_matriz.svg)

:::

???

??? Atividade
Vamos pegar como exemplo a lista não ordenada `md [1, 4, 3]`. Como ficaria a apresentação desta lista na matriz?
::: Gabarito

```
1: 1000
4: 1111
3: 1110
```

:::
???

??? Atividade

E para fazermos isso, qual lógica será utilizada? Escreva um pseudo-código que vai representar a lógica que será utilizada para criar esta matriz.

!!! Dica
Pense em uma matriz de matrizes.
!!!

::: Gabarito
O gabarito abaixo é uma possibilidade para implementar o algoritmo. Discuta com o professor se sua solução é viável.

```txt
# Econtrando o maior valor

maior_valor = 0;
para cada valor do input
    se o valor for maior que o maior_valor
        maior_valor = valor

# Criando uma matriz de tamanho do quantidade de números x maior valor com valor 0

para cada item até a quantidade de números da array.
    para cada valor que vai de 0 até o maior_valor
        matriz[item,valor] = 0

# Trocando a quantidade de 0s referente a um número por 1 na matriz criada.

para cada item até a quantidade de números da array.
    para cada valor até o último número da array.
        matriz(item, valor) = 1;
```

:::

???

??? Atividade
Agora que você já conseguiu implementar a matriz, pense em como ela ficaria no mesmo modelo da atividade anterior e depois, com a gravidade aplicada.

::: Gabarito
Abaixo, podemos conferir a matriz original, sem qualquer alteração.

![](abaco143.svg)

Abaixo, teremos a matriz com a gravidade aplicada.

![](abaco134.svg)

:::
???

Implementando o código em Python, temos:

```python
def bead_sort(obj):
    if all([type(x) == int and x >= 0 for x in obj]):
        ref = [range(x) for x in obj]
    else:
        raise ValueError("Todos os elementos devem ser inteiros não negativos.")
    inter = []
    ind = 0
    prev = sum([1 for x in ref if len(x) > ind])
    while prev:
        inter.append(range(prev))
        ind += 1
        prev = sum([1 for x in ref if len(x) > ind])
```

Bem, vamos recapitular visualmente o que já fizemos:

:matriz

1. Representamos os números em um ábaco.

2. Fizemos analogia a uma matriz.

3. Implementamos esta matriz semelhantemente ao ábaco.

!!! Pit-Stop
O que falta agora? Exatamente! Implementar a gravidade!
!!!

??? Checkpoint 2

Pense em um artifício para `md simular` a gravidade em nosso código e escreva a lógica.

::: Gabarito

```txt
para cada valor de 0 até o maior_valor
    sum_value = 0;
    para cada i de 0 até o total de numeros da array.
        sum_value += matriz[i][valor];
        matriz[i][valor] = 0;

    for (i = len - sum_value; i < len; i++)
    para cada i do total de numeros da array menos a soma até o total de numeros da array.
        matriz[i][valor] = 1;
```

:::

???

Agora que possuímos a lógica acima, chegou a hora de implementá-la em Python, mas antes, vejamos como ficou a matriz após a lógica:

```
1: 1000
3: 1110
4: 1111
```

Abaixo, podemos ver como temos nossa matriz:

![](matriz_final.png)

Lógica implementada em Python.

```python
ind = 0
    prev = sum([1 for x in inter if len(x) > ind])
    out = []
    while prev:
        out.append(prev)
        ind += 1
        prev = sum([1 for x in inter if len(x) > ind])
    out = out[::-1]
```

Por último, temos o código completo implementado em Python, já com a gravidade implementada e a alteração da lista recebida pela lista ordenada pelo **Gravity Sort**.

```python
def bead_sort(obj):
    if all([type(x) == int and x >= 0 for x in obj]):
        ref = [range(x) for x in obj]
    else:
        raise ValueError("Todos os elementos devem ser inteiros não negativos.")
    inter = []
    ind = 0
    prev = sum([1 for x in ref if len(x) > ind])
    while prev:
        inter.append(range(prev))
        ind += 1
        prev = sum([1 for x in ref if len(x) > ind])
    ind = 0
    prev = sum([1 for x in inter if len(x) > ind])
    out = []
    while prev:
        out.append(prev)
        ind += 1
        prev = sum([1 for x in inter if len(x) > ind])
    out = out[::-1]
    return out
```

## Complexidade de tempo da implementação matricial

Na implementação matricial do algoritmo, recebemos um vetor e começamos o transformando em uma matriz de 0s e 1s.

O vetor

```txt
[3, 2, 5, 4, 1, 6]
```

se transformará na matriz:

```txt
[

    [ 1 1 1 0 0 0 ] (3)

    [ 1 1 0 0 0 0 ] (2)

    [ 1 1 1 1 1 0 ] (5)

    [ 1 1 1 1 0 0 ] (4)

    [ 1 0 0 0 0 0 ] (1)

    [ 1 1 1 1 1 1 ] (6)

]
```

Para cada item do vetor, criamos um novo vetor de tamanho S, onde S é o maior número do vetor original.

??? Checkpoint 1

Qual é a complexidade de tempo dessa operação?

::: Resposta
A complexidade dessa operação é O(n \* S)
:::

???

Agora vamos iterar por todos os vetores da matriz, começando de baixo pra cima, e para todo valor '1' encontrado, se houver um valor '0' no mesmo índice porém no vetor abaixo, vamos trocar os valores dos dois vetores no índice comparado.

Vamos repetir esse procedimento até não houver nenhuma troca de valores entre os vetores da matriz.

No final das contas, essa operação simula uma queda dos valores '1' para baixo.

??? Checkpoint 2

Qual é a complexidade de tempo dessa operação?

::: Resposta
A complexidade desse procedimento é O(S _ n^2), pois iteramos por todo número de cada vetor na matriz[n _ S], e precisamos fazer essa iteração até não ter mais modificações para serem feitas.
:::

???

## Complexidade de memória da implementação matricial

Na implementação matricial , precisamos instanciar uma matriz para representar o ábaco.

??? Checkpoint 1

Quais serão as dimensões dessa matriz?

::: Resposta
As dimensões dessa matriz será n x S, sendo S o maior número do nosso vetor de entrada, e n a quantidade de números.
:::

???

O espaço alocado para essa matriz é justamente a complexidade de memória para o nosso algoritmo, O(n\*S).

## Implementação em vetor

A segunda implementação que vamos mostrar é a utilizando um vetores.  Resgatando a explicação anterior de matrizes, vamos usá-la como ponto de partida. Precisamos encontrar alguma forma de transformar aquela matriz de uns e zeros em um único vetor.

??? Checkpoint 1

Você tem alguma ideia de como isso pode ser feito?

!!! Dica

Use a ideia da matriz como base.

!!!

::: Gabarito

Isso mesmo, somando as linhas da matriz. Se pensarmos bem, temos varios vetores empilhados, e para reunirmos todos os dados em um único vetor basta somar toda a informação.

:vetor_check1

???

Detalhando um pouco mais o processo anterior e resgatando a criação da matriz temos as seguintes etapas:

1. Transformar as linhas em vetores de tamanho igual ao maior valor do input com zeros;

2. Preencher as posições da esquerda para a direita com 1 na quantidade de pedaços nas linhas;

3. Somar as linhas em um único vetor;

Aqui temos a visualização do processo completo.

:vetor


Aqui temos o pseudo código do processo que construímos até agora.

```txt
# Input do algoritmo -> [1, 4, 3]


# Econtrando o maior valor

maior_valor = 0;
para cada valor do input
    se o valor for maior que o maior_valor
        maior_valor = valor

# Criando o vetor auxiliar de tamanho do maior_valor

vetor_auxiliar = [0, 0, 0, 0]
para cada index do valor do input
    para cada valor que vai de 0 até o input[index]
        vetor_auxiliar[valor] += 1


# Resultado -> [3, 2, 2, 1]
```

Com o nosso vetor em mãos, precisamos agora extrair os números deles em ordem. Porém, como podemos fazer isso?

??? Construção de raciocínio

Sabemos que todos os nossos números estão somados dentro desse único vetor, agora como podemos extrair um número por vez desse montante?

!!! Dica

Lembre da ideia que cada linha era um vetor e nós "achatamos" a nossa matriz (ou a nossa pilha de vetores) em um único vetor. Podemos então retirar de algum jeito vetor a vetor desse único vetor.

!!!

::: Resposta

Voltando para a alusão do ábaco, podemos retirar a última linha e contar o número de bolas. Abaixo temos uma melhor visualização desse processo.

:vector_loop2

Com isso, precisamos aplicar essa alusão do ábaco ao nosso vetor.

???

??? Construção de raciocínio

O que significa remover a última linha do ábaco em relação ao nosso vetor?

!!! Dica

Pense no processo de subtração, porém faça algumas restrições para não encontrar valores negativos.

!!!

::: Resposta

A ideia aqui é subtrairmos um de todas as casas em que temos valores maiores que zero. Sempre que essa condição for contemplada, vamos somar as casas onde ocorreu subtração e assim encontrar o valor a ser ordenado. Abaixo temos uma melhor visualização desse processo:

:vetorsub1


???

Aqui temos um pseudo código gerado com o raciocínio construído acima.

```txt
# Input da segunda etapa -> [3, 2, 2, 1]


para todo valor i menor que o tamanho do input
    para todo valor j menor que o maior valor
        input[i] = j
        lista transposta[j] -= 1

para todo valor i menor que metade do tamanho do input
    j = input[i]
    input[i] = input[n - i - 1]
    input[n - i - 1] = j


# Resultado -> [4, 3, 1]
```

Um detalhe importante de mencionarmos é que a dimensão do vetor de saída será igua ao maior valor encontrar no input.


## Complexidade de tempo da implementação vetorial

A implementação vetorial do Bead Sort pode ser dividida em duas etapas.

A primeira etapa é a criação do vetor auxiliar.

O pseudo-código para criação desse vetor é o seguinte:

```
vetor_auxiliar = [0] * max(vec)

for num in vec:
    for i in range(num):
        vetor_auxiliar[i] += 1
```

Analisando esse pseudo código, podemos notar dois loops.

O primeiro loop tem o número de iterações = n, enquanto o segundo tem número de iterações máximo = S = maior número dentro do vetor.

??? Checkpoint 1

Qual é a complexidade de tempo dessa operação?

::: Resposta
O (n\*S)
:::

???

A segunda parte da implementação consiste em e subtrair '1' de todo elemento do vetor auxiliar de forma iterada. a cada iteração, contamos o número de valores maiores que 0 presente no vetor auxiliar.

??? Checkpoint 2

Qual é a complexidade de tempo dessa operação?

::: Resposta
A complexidade dessa etapa também é O(n\*S)
:::

???

Assim, A complexidade da implementação vetorial é O(2*n*S) = O(n\*S).

## Complexidade de memória da implementação vetorial

Na implementação vetorial,o único espaço de memória que precisamos alocar é o espaço para armazenar o vetor.

??? Checkpoint 1

Qual é o tamanho que esse vetor precisa ter?

::: Resposta
O vetor precisa ter o tamanho igual ao maior valor do vetor de entrada.
:::

???

Como o único espaço de memória a ser alocado ao algoritmo é o espaço desse vetor, a complexidade será O(S).

## Desafio

??? Desafio Bead Sort 1

Escreva o vetor auxiliar do bead sort a partir do vetor de entrada [6, 3, 5, 1, 7, 3, 8, 2]

::: Gabarito

Vetor Auxiliar: [ 8, 7, 6, 4, 4, 3, 2, 1 ]

:::

???

??? Desafio Bead Sort 2

A partir do vetor auxiliar obtido acima, escreva como fica o vetor auxiliar no final de cada iteração do bead sort

::: Gabarito

Iteração 1: [7, 6, 5, 3, 3, 2, 1, 0]
Iteração 2: [6, 5, 4, 2, 2, 1, 0, -1]
Iteração 3: [5, 4, 3, 1, 1, 0, -1, -2]
Iteração 4: [4, 3, 2, 0, 0, -1, -2, -3]
Iteração 5: [3, 2, 1, -1, -1, -2, -3, -4]
Iteração 6: [2, 1, 0, -2, -2, -3, -4, -5]
Iteração 7: [1, 0, -1, -3, -3, -4, -5, -6]
Iteração 8: [0, -1, -2, -4, -4, -5, -6, -7]

:::

???
