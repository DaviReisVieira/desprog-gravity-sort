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

??? Checkpoint 1

Pense em algo que possamos utilizar para `md substituir` o ábaco na hora de programar.

::: Gabarito
Imagine que cada bolinha do ábaco é um elemento e cada haste é um vetor. Exato! Um ábaco pode ser visto como uma matriz de `md TRUE` e `md FALSE`, ou `md 1s` e `md 0s`.
:::

???
Vamos pegar como exemplo a lista não ordenada `md [1, 4, 3]`. Agora, vamos adicionar estes números ao ábaco, assim como no site exemplo, só que ao invés das bolinhas, teremos 1, e nos espaços vazios, 0.

```
1: 1000
4: 1111
3: 1110
```

Para fazermos isso, pensemos na seguinte lógica:

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

Implementando o código em C, temos:

```c
void bead_sort(int *a, int len)
{
    int i, j, max_value, sum_value;
    unsigned char *beads;
#define BEAD(i, j) beads[i * max_value + j]

    for (i = 1, max_value = a[0]; i < len; i++){
        if (a[i] > max_value){
            max_value = a[i];
        }
    }

    beads = calloc(1, max_value * len);

    for (i = 0; i < len; i++){
        for (j = 0; j < a[i]; j++){
            BEAD(i, j) = 1;
        }
    }
}
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

Agora que possuímos a lógica acima, chegou a hora de implementá-la em C, mas antes, vejamos como ficou a matriz após a lógica:

```
1: 1000
3: 1110
4: 1111
```

Abaixo, podemos ver como temos nossa matriz:

![](matriz_final.png)

Lógica implementada em C.

```c
for (j = 0; j < max_value; j++)
{
    /* conta quantas bolinhas há em cada haste */
    for (sum_value = i = 0; i < len; i++)
    {
        sum_value += BEAD(i, j);
        BEAD(i, j) = 0;
    }
    /* coloca as bolinhas abaixo da soma */
    for (i = len - sum_value; i < len; i++)
        BEAD(i, j) = 1;
}
```

Por último, temos o código completo implementado em C, já com a gravidade implementada e a alteração da lista recebida pela lista ordenada pelo **Gravity Sort**.

```c
void bead_sort(int *a, int len)
{
    int i, j, max_value, sum_value;
    unsigned char *beads;
#define BEAD(i, j) beads[i * max_value + j]

    for (i = 1, max_value = a[0]; i < len; i++)
        if (a[i] > max_value)
            max_value = a[i];

    beads = calloc(1, max_value * len);

    for (i = 0; i < len; i++)
        for (j = 0; j < a[i]; j++)
            BEAD(i, j) = 1;

    for (j = 0; j < max_value; j++)
    {
        for (sum_value = i = 0; i < len; i++)
        {
            sum_value += BEAD(i, j);
            BEAD(i, j) = 0;
        }
        for (i = len - sum_value; i < len; i++)
            BEAD(i, j) = 1;
    }

    for (i = 0; i < len; i++)
    {
        for (j = 0; j < max_value && BEAD(i, j); j++)
            ;
        a[i] = j;
    }

}
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
A complexidade dessa operação é O(n * S)
:::

???

Agora vamos iterar por todos os vetores da matriz, começando de baixo pra cima, e para todo valor '1' encontrado, se houver um valor '0' no mesmo índice porém no vetor abaixo, vamos trocar os valores dos dois vetores no índice comparado.

Vamos repetir esse procedimento até não houver nenhuma troca de valores entre os vetores da matriz.

No final das contas, essa operação simula uma queda dos valores '1' para baixo.

??? Checkpoint 2

Qual é a complexidade de tempo dessa operação?

::: Resposta
A complexidade desse procedimento é O(S * n^2), pois iteramos por todo número de cada vetor na matriz[n * S], e precisamos fazer essa iteração até não ter mais modificações para serem feitas.
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

O espaço alocado para essa matriz é justamente a complexidade de memória para o nosso algoritmo, O(n*S).

## Implementação em vetor

A segunda implementação que vamos mostrar é a utilizando um vetor. Ao trocarmos seu eixo e deixarmos as peças caírem, estamos ordenando visualmente.

??? Checkpoint 1

Como podemos transformar esse raciocínio em código?

!!!

Tente adpatar a explicação de matriz

!!!

::: Gabarito

Vamos pegar todo o raciocínio da matriz, mas agora vamos somar as linhas da matriz, resultando em um único vetor.

???

Para isso iremos transformar cada linha em um vetor de 0 ou 1 em que a quantidade de 1 é o valor da linha e então somar as linhas.

:vector

Detalhando um pouco mais temos as seguintes etapas:

1. Transformar as linhas em vetores de tamanho igual ao maior valor do input com zeros;

2. Preencher as posições da esquerda para a direita com 1 na quantidade de pedaços nas linhas;

3. Somar as linhas em um único vetor;


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

??? Construção de reciocínio

Sabemos que todos os nossos números estão somados dentro desse único vetor, agora como podemos extrair um número por vez desse montante?

!!! Dica

Pense novamente no ábaco após a gravidade ter feito efeito sobre nossas peças.

!!!

::: Resposta

Voltando para a alusão do ábaco, podemos retirar a última linha e contar o número de bolas. Abaixo temos uma melhor visualização desse processo.

:vector_loop2

Com isso, precisamos transformar essa alusão para o caso do nosso vetor.

???

??? Construção de raciocínio

O que significa remover a última linha em termos de código?

::: Resposta

Podemos remover um de todos os items do vetor e em todos os casos que o resultado seja maior que zero, temos um valor válido. Portanto, precisamos apenas somar a quantidade de valores validos encontrados. Abaixo temos uma ilustração desse processo.

:vector2code

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
O (n*S)
:::

???

A segunda parte da implementação consiste em e subtrair '1' de todo elemento do vetor auxiliar de forma iterada. a cada iteração, contamos o número de valores maiores que 0 presente no vetor auxiliar.

??? Checkpoint 2

Qual é a complexidade de tempo dessa operação?

::: Resposta
A complexidade dessa etapa também é O(n*S)
:::

???

Assim, A complexidade da implementação vetorial é O(2*n*S) = O(n*S).

## Complexidade de memória da implementação vetorial

Na implementação vetorial,o único espaço de memória que precisamos alocar é o espaço para armazenar o vetor.

??? Checkpoint 1

Qual é o tamanho que esse vetor precisa ter?

::: Resposta
O vetor precisa ter o tamanho igual ao maior valor do vetor de entrada.
:::

???

Como o único espaço de memória a ser alocado ao algoritmo é o espaço desse vetor, a complexidade será O(S).


Desafio
-------

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