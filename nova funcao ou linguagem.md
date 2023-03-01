# Usar uma função da linguagem ou criar uma nova?

A maioria das linguagens de programação são para uso geral. Sendo assim, costumam incluir um grande número de funções e procedimentos seja na própria linguagem seja por meio de bibliotecas. Para "facilitar" a vida, geralmente o programador utiliza esses procedimentos para resolver os problemas apresentados. Mas será que é a melhor forma de resolver os problemas? Vejamos um caso específico. 

## Problema

Apareceu no twitter uma rotina bobinha em JS com a finalidade de retornar o segundo maior número de uma série dada. Fiz uma rotina interessante mas que falhou em alguns casos. Em vez de perder o meu tempo para corrigi-la, como eu não uso JS mesmo, resolvi fazer em outra linguagem. Que tal Nim que é legalzinha, quase um Python compilado? Então, vamos lá.

## Rápido e errado

Basta ordenar a entrada de dados e pegar o penúltimo elemento para termos o segundo maior. Por enquanto, iremos assumir que foi passada uma sequência com dois ou mais elementos diferentes.

```python
proc segundoMaior (nums : seq[int]):int =
    result = nums.sorted[^2]
```

Foi passada uma sequência para a função, ela foi ordenada e pegamos o segundo elemento do final para o início **[^2]** (em Ruby/Python, por exemplo, seria **[-2]**).

Mas se a amostra tiver itens duplicados poderemos ter problemas. Pode ocorrer que o maior número seja duplicado portanto, o penúltimo será igual ao último.

## Primeiro refinamento

O mais fácil seria eliminar os número duplicados. Fácil. Usamos o **deduplicate** (outras linguagens pode ser **unique** ou **uniq** como no Ruby). Pronto.

```python
proc segundoMaior (nums : seq[int]):int =
    result = nums.sorted.deduplicate[^2]
```

## Pensando no tempo de execução

A forma de eliminar duplicatas em uma série, seria pegar o primeiro número e comparar com todos os outros para ver se existe algum duplicado. A mesma coisa para o segundo. Este procedimento lembra muito o Bubble Sort que é o pior método de ordenação. Resolvi testar com 100, 1000, 10000 e 100000. 

```python
proc segundoMaior (nums : seq[int]):int =
    result = nums.sorted.deduplicate[^2]

timeOnce("teste 100"):
    discard segundoMaior(toSeq(1..100))
timeOnce("teste 1000"):
    discard segundoMaior(toSeq(1..1_000))
timeOnce("teste 10000"):
    discard segundoMaior(toSeq(1..10_000))
timeOnce("teste 100000"):
    discard segundoMaior(toSeq(1..100_000))
```

Os tempos foram:

```bash
teste 100 -> [17μs 476.00ns]
teste 1000 -> [451μs 511.00ns]
teste 10000 -> [38ms 328μs 412.00ns]
teste 100000 -> [3s 768ms 914μs 629.00ns]
```

Multiplicando-se a amostra por dez, o tempo aumentava exponencialmente. Mas os dados já estavam ordenados, poderia existir uma rotina mais eficiente. Se o próximo dados for maior, não precisamos continuar verificando se existe um número igual até o final da amostra. A rotina deduplicate possui o parâmetro **(isSorted = true)** exatamente para esses casos. 

```python
proc segundoMaior (nums : seq[int]):int =
    result = nums.sorted.deduplicate(isSorted = true)[^2]
```

E os tempos?

```bash
teste 100 -> [12μs 535.00ns]
teste 1000 -> [30μs 476.00ns]
teste 10000 -> [307μs 268.00ns]
teste 100000 -> [3ms 370μs 6.00ns]
```

Os tempos melhoram assustadoramente.

## Refazendo a rotina

Mas ainda não estou contente com a rotina. Poderia escrever uma rotina eliminando a parte de ordenação e eliminação dos duplicados. E se eu usar uma espécie de Bubble Sort para pegar apenas os dois maiores números enquanto estão sendo tratados? Parece ser melhor, mas só testando.

```python
proc segundoMaior (nums : seq[int]):int =
    var
        menor, maior =  low(int)
    for val in nums:
        if val > maior:
            menor = maior
            maior = val
        else:
            if val < maior and val > menor:
                menor = val
    result = menor
```

A rotina ficou maior e mais difícil de ler. Curioso para saber o resultado? Vamos lá.

```bash
teste 100 -> [2μs 557.00ns]
teste 1000 -> [7μs 520.00ns]
teste 10000 -> [76μs 899.00ns]
teste 100000 -> [759μs 190.00ns]
teste 1000000 -> [8ms 414μs 553.00ns]
teste 10000000 -> [82ms 75μs 474.00ns]
```

Melhoraram tanto que coloquei 10.000.000. E note que são os tempos para o pior caso.

## Conclusão

Nenhuma conclusão. Só que **neste caso** conseguimos uma melhora significativa utilizando a ideia de uma rotina que não é eficiente para o propósito para que foi criada mas foi implementada com sucesso para um caso específico.

## Erros

Uma das vantagens das rotinas iniciais é que seria gerada uma exceção em dois casos de erro onde não existiriam o segundo maior elemento:

1. amostras com menos de dois elementos
2. amostras com todos os elementos iguais

A minha rotina não trabalha com índices. Se for enviado menos de dois items ou, se todos os items forem iguais, o retorno será **low(int)**. Então, se foram enviados menos de dois dados ou o retorno é low(int), é só gerar uma exceção. O resultado final, com os testes, ficou assim:

```python
import sequtils, unittest

proc segundoMaior (nums : seq[int]):int =
    var
        menor, maior = low(int)
    for val in nums:
        if val > maior:
            menor = maior
            maior = val
        else:
            if val < maior and val > menor:
                menor = val
    result = menor
    if menor == low(int):
        raise newException(ValueError,"Não existe segundo maior.")

when isMainModule:
    suite "Segundo maior":
        test "Valores corretos":
            check segundoMaior(toSeq(1..1_000_000)) == 999_999
            check segundoMaior(@[1,6,66,62,66,66,7,8,8]) == 62
            check segundoMaior(@[7,6,5,4,3,2,1]) == 6
            check segundoMaior(@[1,2,3,4,5,6,7,8]) == 7
            check segundoMaior(@[1,6,6,7,7,8,8]) == 7
            check segundoMaior(@[3,2]) == 2
        test "Erros":
            expect ValueError:
                discard segundoMaior(@[])
            expect ValueError:
                discard segundoMaior(@[1])
            expect ValueError:
                discard segundoMaior(@[2,2,2,2,2])
```

Ok, passou em tudo. O que não significa que não possa ter erros. 

```bash
[Suite] Segundo maior
  [OK] Valores corretos
  [OK] Erros
```
