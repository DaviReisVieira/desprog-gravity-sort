# Bead Sort

## Introdução

Existem diversos tipos de algoritmos de comparação. Ente eles temos:

- Selection sorts: Selection sort, Heapsort, Smoothsort...

- Insertion sorts: Insertion sort Shell sort Splay sort...

- Distribution sorts: American flag sort, Bead sort, Bucket sort...

De modo geral, algoritmos de distribuição repartem os dados em diferentes espaços de memória (buckets), que são usados, posteriormente, para compor o output.

Para iniciar a explicação do Bead Sort, precisamos pensar em um ábaco.

![](abaco.png)

De modo geral, ele possui todas as fileiras com a mesma quantidade de unidades e o usuário passa os pedaços de um lado para o outro fazendo algum tipo de contagem. No nosso caso, vamos pensar que as linhas são compostas apenas pela parte relevante, desconsiderando as unidades que sobram e não fazem parte do raciocínio. Considerando que as peças do lado esquerdo são as relevantes, podemos visualizar nosso ábaco da seguinte maneira.

![](meio_abaco.png)

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

    for (i = 1, max_value = a[0]; i < len; i++)
        if (a[i] > max_value)
            max_value = a[i];

    beads = calloc(1, max_value * len);

    for (i = 0; i < len; i++)
        for (j = 0; j < a[i]; j++)
            BEAD(i, j) = 1;
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

## Implementação em vetor

A segunda implementação que vamos mostrar é a utilizando um vetor. Nesta estratégia iremos pensar usando a ideia do ábaco. Ao trocarmos seu eixo e deixarmos as peças caírem, estamos ordenando visualmente. No entanto, como podemos transformar esse raciocínio em código?

Como estamos implementando em vetor, precisamos transformar as linhas do ábaco em um único vetor. Para isso iremos transformar cada linha em um vetor de 0 ou 1 em que a quantidade de 1 é o valor da linha e então somar as linhas.

![](vector_loop1.png)

Detalhando um pouco mais temos as seguintes etapas:

1. Transformar as linhas em vetores de tamanho igual ao maior valor do input com zeros;

2. Preencher as posições da esquerda para a direita com 1 na quantidade de pedaços nas linhas;

3. Somar os vetores em um único vetor;

Na terceira parte do desenho temos a visualização do vetor que encontramos no processo feito acima. Seria como se tivéssemos invertido os eixos e então estivéssemos contando a quantidade de peças nas colunas.

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

Com a implementação lógica feita, podemos partir para a implementação em código:

```c
void bead_sort(int *a, int n)
{

    int i, j, max_value;

    for (i = 1, max_value = a[0]; i < n; i++)
        if (a[i] > max_value)
            max_value = a[i];

    int *transposed_list = calloc(max_value, sizeof(int));

    for (i = 0; i < n; i++)
    {

        for (j = 0; j < a[i]; j++)
            transposed_list[j]++;
    }
}
```

Porém, nossos valores ainda não estão ordenados. A última etapa consiste em removermos a linha mais baixa do ábaco e contar o número de peças para assim encontrarmos o maior valor referente a nossa ordenação.

:vector_loop2

Com o processo mais claro, podemos partir para a pseudo-implementação em código. Nessa parte, passamos por todos os itens do vetor e sempre que ele for maior que zero, subtraímos em 1 o seu valor, e adicionamos 1 a uma variável de suporte que resultará no valor ordenado da iteração.

```txt
# Input da segunda etapa -> [3, 2, 2, 1]


# pseudo codigo [esperando codigo do Davi]


# Resultado -> [4, 3, 1]
```

Com isso, temos nosso pseudo código implementado e podemos partir para a implementação efetiva em C.

```c
void bead_sort(int *a, int n)
{

    int i, j, max_value;

    for (i = 1, max_value = a[0]; i < n; i++)
        if (a[i] > max_value)
            max_value = a[i];

    int *transposed_list = calloc(max_value, sizeof(int));

    for (i = 0; i < n; i++)
    {

        for (j = 0; j < a[i]; j++)
            transposed_list[j]++;
    }

    for (i = 0; i < n; i++)
    {
        for (j = 0; j < max_value && transposed_list[j]; j++)
            ;
        a[i] = j;
        for (j = 0; j < max_value && transposed_list[j]; j++)
            transposed_list[j]--;
    }

    for (i = 0; i < n / 2; i++)
    {
        j = a[i];
        a[i] = a[n - i - 1];
        a[n - i - 1] = j;
    }
}
```

## Complexidade de tempo da implementação matricial

Na implementação matricial do algorítimo, recebemos um vetor e começamos o transformando em uma matriz de 0s e 1s.

O vetor 

[3, 2, 5, 4, 1, 6]


se transformará na matriz

[

    [ 1 1 1 0 0 0 ] (3)

    [ 1 1 0 0 0 0 ] (2)

    [ 1 1 1 1 1 0 ] (5)

    [ 1 1 1 1 0 0 ] (4)

    [ 1 0 0 0 0 0 ] (1)

    [ 1 1 1 1 1 1 ] (6)

]

A complexidade dessa operação é O(n * S), pois para cada item do vetor, criamos um novo vetor de tamanho S, onde S é o maior número do vetor original.

Agora vamos iterar por todos os vetores da matriz, começando de baixo pra cima, e para todo valor '1' encontrado, se houver um valor '0' no mesmo índice porém no vetor abaixo, vamos trocar os valores dos dois vetores no índice comparado.

Vamos repetir esse procedimento até não houver nenhuma troca de valores entre os vetores da matriz.

No final das contas, essa operação simula uma queda dos valores '1' para baixo.

A complexidade desse procedimento é O(S * n^2), pois iteramos por todo número de cada vetor na matriz[n * S], e precisamos fazer essa iteração até não ter mais modificações para serem feitas.

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

O primeiro loop tem o número de iterações = n, enquanto o segundo tem número de iterações máximo = s = maior número dentro do vetor.

Dessa forma, a complexidade temporal da primeira parte da implementação vetorial é O (n*S).

A segunda parte da implementação consiste em e subtrair '1' de todo elemento do vetor auxiliar de forma iterada. a cada iteração, contamos o número de valores maiores que 0 presente no vetor auxiliar.

A complexidade dessa etapa também é O(n*S)

Assim, A complexidade da implementação vetorial é O(2*n*S) = O(n*S).

Desafio
-------

??? Desafio Bead Sort 1

Escreva o vetor auxiliar do bead sort a partir do vetor de entrada [6, 3, 5, 1, 7, 3, 8, 2]

::: Gabarito

Vetor Auxiliar: [  ]

:::

???

??? Desafio Bead Sort 2

A partir do vetor auxiliar obtido acima, escreva como fica o vetor auxiliar no final de cada iteração do bead sort

::: Gabarito

Iteração 1: [  ]
Iteração 2: [  ]
Iteração 3: [  ]
Iteração 4: [  ]
Iteração 5: [  ]
Iteração 6: [  ]
Iteração 7: [  ]

:::

???