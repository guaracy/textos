# Icon



# DRAFT

# EM CONSTRÇÃO





> "... Icon uses the concepts of **success** and **failure** in combination with **generators** to perform goal-directed evaluation. If a computation fails, alternative values from generators are produced automatically in an attempt to produce an overall successful result. ..."



A linguagem [Icon](https://www2.cs.arizona.edu/icon/https://www2.cs.arizona.edu/icon/) é uma linguagem de uso geral tendo como seu principal criador [Ralph E. Griswold](https://www2.cs.arizona.edu/~ralph/) (sempre é interessante saber um pouco sobre o criador da linguagem). Na aparência lembra Pascal e C, mas a citação no início do texto faz toda a diferença como veremos depois. 

Para quem deseja se aventurar um pouco na linguagem, talvez seja mais interessante a linguagem [Unicon](http://www.unicon.org/) que é basicamente, Icon com a possibilidade de orientação ao objeto e continua com uma certa atividade. 

## Hello World

```objectpascal
procedure main()
  write("Hello, world")
end
```

Exemplo besta apenas para mostrar a sintaxe *semelhante* ao Pascal e C.  Mas possui diversas melhorias como não necessitar o BEGIN desnecessário após a definição da procedure (ou a chave no caso do C) e não precisar dos ponto e vírgula superfluos do C e do Pascal. Infelizmente ainda necessita do END para fechar o bloco. Para elimina-lo, só se usasse a indentação de [ABC](https://homepages.cwi.nl/~steven/abc/) (que foi utilizado por Python e Beads, por exemplo). 

Concordo plenamente que só essas pequenas melhorias não seriam necessárias para chamar a minha atenção para a linguagem. Vamos voltar para a citação inicial para entender melhor o significado e utilização de:

## Sucesso e Falha

Não tem mistério. Uma operação pode ser bem sucedida e retornar um valor que representa o sucesso ou pode falhar e retornar ```&fail```. *Ah, é o mesmo que falso e verdadeiro*. Não. É um valor para o sucesso ou &fail para falha. Vejamos um exemplo:

```objectpascal
  if 3 > 5 then write("maior") else write("menor")
  if 9 > 5 then write("maior") else write("menor")
```

retorna:

```
menor
maior
```

*Ah, viu! Eu falei que era o mesmo que falso e verdadeiro*. Calma lá. A primeira expressão, ```3 > 5``` retorna ```&fail``` e a segunda expressão ```9 > 5``` retorna ```5```. Isto permite escrever uma expressão como:

```objectpascal
if 9 > 5 > 3 then write("5 está entre 9 e 3)
```

Em outras linguagens, o ```9 > 5``` retornaria ```True``` e não seria possível saber se é maior ou menor do que ```3```.  Vejamos outra construção interessante:

```objectpascal
  a := 5
  b := 6
  write("soma de a + c = " , c := a + b)
  write("c = ",c)
```

como resultado temos:

```
soma de a + c = 11
c = 11
```

O valor de ```c``` foi atribuido dentro do próprio ```write``` e retornou o valor para ser impresso. Outras linguagens dirão que você é burro e não irão aceitar.  Agora vamos ver outro aspecto que confere poder à linguagem:

## Geradores

Se pensou que é algo que gera alguma coisa, acertou. Um gerador de eletricidade, var gerar eletricidade para um determinado fim. No caso de uma linguagem, o gerador vai gerar valores que serão consumidos por outros procedimentos. 

Um exemplo simples são os laços exestentes em diversas linguagens. Por exemplo, ```for i:=1 to 10 do ...``` irá gerar valores de 1 até 10 que serão utilizados por algum procedimentos. Ou ```for i in [1,3,5,7,9] do ... ``` com a mesma finalidade.

Icon tem um bom número de geradores. Um deles é o ponto de exclamação (**!**) que, em alguns casos, equivaleria ao ```for x in ```.  Por exemplo:

```objectpascal
!"abcd"    # retorna a,b,c,d
![1,2,7,9] # retorna 1,2,7,9
!5         # retorna 1,2,3,4,5
!5.34      # retorna 5, . ,3,4
```

Outro é o *alternation* que é a barra vertical (**|**) e retorna os valores na sequência em que forem informados.

```objectpascal
(11 | 4 | "abc" | 3.2 |2K ) # retorna 11, 4, abc, 3.2, 2048
```

Também seria possível fazer uma atribuição em um ou mais termos mas deverá estar entre parêntesis ```... | (c := 3.2) | ...```. Agora conseguimos uma construção interessante que é ```(x|y) > (3|4)```. Em uma linguagem tradicional equivale a ```if x>3 or y>3 or x>4 or y>4```.

Mais uma curiosidade. Podemos chamar uma função diversas vezes alterando o argumento. O programa abaixo retorna ```6,10,8,2,12```

```objectpascal
procedure dobro(v)
  return v * 2
end

procedure main()
  every write(dobro(3|5|4|1|6))
end
```

Também temos o ```exp1 to exp2 [by exp3] ``` 

```objectpascal
1 to 6        # retorna 1,2,3,4,5,6
1 to 6 by 2   # retorna 1,3,5
6 to 1 by -2  # retorna 6,5,4,3,2,1
```

Note que os geradores não retornam todos os valores de uma só vez. Eles são produzidos a medida em que são consumidos. Então exite o ```every``` que seria como o ```for``` de outras linguagens. Por exemplo, ```write(1 to 5)``` irá imprimir apenas um ```1```. Para tanto, você deverá escrever:

```objectpascal
every i:= 1 to 5 do write(i)
```

ou, se preferir, poderá utilizar os recursos da linguagem e escrever:

```objectpascal
every write(1 to 5)
```

Além de ficar menor, eliminamos a variável que servia só para adequar ao nosso antigo modo de escrever programas.








