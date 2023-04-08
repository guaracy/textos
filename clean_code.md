# Clean Code

Em algum tuíte sobre clean code eu falei que a legibilidade depende uns 80% da linguagem. É claro que um bom programador consegue deixar qualquer linguagem ilegível. A primeira (e talvez a única) linguagem de programação de alto nível que nasceu com o objetivo de ser legível foi o COBOL.

Um dos problemas das linguagens é que são para uso geral e os usuários ficam querendo coisinhas para que a linguagem possa competir com outra. Então vão se enchendo de frescuras. Na hora de resolver um problema, existem muito mais opções do que o desejável. Ultimamente COBOL também vem sendo adornada.

Resolvi colocar alguns programas do exercício [100 doors](https://rosettacode.org/wiki/100_doors) do Rosetta Code. É um exemplo simples mas vamos ver como se comportam algumas linguagens (o código abaixo foi baseado na versão que está na página): 

##### COBOL

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. 100-DOORS.

       DATA DIVISION. 
       WORKING-STORAGE SECTION.
       01 WS-INDICES.
          05 I               PIC 9(3).
          05 J               PIC 9(3).
       01 LAST-DOOR          PIC 9(3) VALUE 100.
       01 DOORS.
          05 DOOR            PIC 9 OCCURS 100 TIMES.
             88 IS-OPEN      VALUE 1.

       PROCEDURE DIVISION.
       
       PERFORM VARYING I FROM 1 BY 1 UNTIL I GREATER THAN LAST-DOOR
          PERFORM VARYING J FROM I BY I  UNTIL J GREATER THAN LAST-DOOR
            SUBTRACT 1 FROM DOOR(J)
          END-PERFORM
          IF IS-OPEN(I)
            DISPLAY "DOOR " I " IS OPEN."
          END-IF
       END-PERFORM
       
       STOP RUN.
```

Se você desejar mais informações sobre COBOL, deverá procurar uma documentação. A primeira coisa que COBOL se diferencia de outras é na estrutura. Primeiro são as *divisões*. Na *IDENTIFICATION DIVISION* serão colocados os elementos para a identificação do programa. Depois tem a *ENVIRONMENT DIVISION* que possui as informações sobre o ambiente (computador, periféricos, etc..). Após temos a *DATA DIVISION* que é a segunda mais importante pois define os arquivos de entrada e saída, as variáveis que serão usadas pelo programa e outras coisas. Em seguida vem a divisão mais importante que é a *PROCEDURE DIVISION*. Contém o código que irá manipular as variáveis para gerar algum resultado. Em 1976, Niklaus Wirth escreveu o livro [Algorithms + Data Structures = Programs](https://en.wikipedia.org/wiki/Algorithms_%2B_Data_Structures_%3D_Programs). Essas separações já trazem uma clareza na estrutura do programa e deixa como uma receita de bolo (ingredientes e preparo). Na *data division* é interessante o nível *88* que facilita a codificação. Em vez de escrever `IF DOOR(I) = 1` se escreve `IF IS-OPEN(I)`. Na vida real poderíamos ter algo como `IF REGIAO-SUDESTE` ou `IF CALCULAR-DESCONTO`.

Na *procedure division*, é só saber um pouco de inglês para entender o que está sendo feito. É claro que eu poderia ter escrito `>` em vez de `GREATER THAN`. Mas onde está `LAST-DOOR`? Lá na *data division*. O programa é pequeno e apenas para exemplo mas o melhor poderia ser colocar o prefixo `WS-` nas variáveis para indicar que estão na *working-storage section* para diferenciar de `SS-` para *screen section* etc.. Também seria interessante colocar *parágrafos* para facilitar a leitura de programas maiores.

##### APL

```apl
⍸≠⌿0=(⍳100)∘.|⍳100
```

Sinceramente não sei o que dizer. Só alguém com conhecimento da linguagem para dizer se é legível ou não. Deve ser. Ou não.

##### B

```c
main()
{
  auto doors[100]; /* != 0 means open */
  auto pass, door;

  door = 0;
  while( door<100 ) doors[door++] = 0;

  pass = 0;
  while( pass<100 )
  {
    door = pass;
    while( door<100 )
    {
      doors[door] = !doors[door];
      door =+ pass+1;
    }
    ++pass;
  }

  door = 0;
  while( door<100 )
  {
    printf("door #%d is %s.*n", door+1, doors[door] ? "open" : "closed");
    ++door;
  }

  return(0);
}
```

Se você não sabe o motivo de eu ter colocado `B` é que `C` é apenas `B`com tipagem. Parece que `B` não tinha [`for`](https://www.thinkage.ca/gcos/expl/b/tuto/tuto.html#Chapter11).

##### BaCon

```basic
OPTION BASE 1

DECLARE doors[100]

FOR size = 1 TO 100
    FOR pass = 0 TO 100 STEP size
	doors[pass] = NOT(doors[pass])
    NEXT
NEXT

FOR which = 1 TO 100
    IF doors[which] THEN PRINT which
NEXT
```

BaCon é apenas um BASIC convertido para C e depois compilado. Mas o código é bem limpo e legível. Na realidade, o último loop poderia ser eliminado. Mas como o pessoal geralmente se baseia em um código...

##### C

```c
#include <stdio.h>

int main()
{
  char is_open[100] = { 0 };
  int pass, door;

  /* do the 100 passes */
  for (pass = 0; pass < 100; ++pass)
    for (door = pass; door < 100; door += pass+1)
      is_open[door] = !is_open[door];

  /* output the result */
  for (door = 0; door < 100; ++door)
    printf("door #%d is %s.\n", door+1, (is_open[door]? "open" : "closed"));

  return 0;
}
```

A inclusão de `C` foi mais para ver como a inclusão do `for` diminuiu a quantidade de linha e, ao meu ver, melhorou a legibilidade do código em relação ao `B`. Por outro lado, forçar o usuário a usar uma matriz tendo como base zero serve para facilitar a vida de quem fez o compilador e dificultar a vida de um programador. Note que é necessário somar 1 no passo do segundo `for` e, para mostrar as portas abertas também é necessário somar 1 caso contrário a resposta ficaria errada dizendo que a porta **zero(?)** estava aberta. 

##### CRYSTAL

```ruby
doors = Array.new(100, false)

1.upto(100) do |i|
  i.step(by: i, to: 100) do |j|
    doors[j - 1] = !doors[j - 1]
  end
end

doors.each_with_index do |open, i|
  puts "Door #{i + 1} is #{open ? "open" : "closed"}"
end

```

Crystal é quase um Ruby compilado. Aqui vale o mesmo comentário do C. Primeiro elemento da matriz é zero. Só que na passagem pelas portas é subtraído 1 do índice e quando mostra se a porta está aberta soma 1 ao índice. Impossível seguir a lógica do problema sem um *workaround*.

##### D

```d
import std.stdio;
const N = 101; // #doors + 1
void main() {
  bool[N] doors = false;
  for(auto door=1; door<N; door++ ) {
    for(auto i=door; i<N; i+=door ) doors[i] = !doors[i];
    if (doors[door]) write(door, " ");
  }
}
```

D surgiu como resposta ao C++. Aqui o autor desperdiçou o primeiro elemento do array em nome da legibilidade do algoritmo. É uma técnica mas é complicada para ser utilizada sempre. Se fosse necessário armazenar os cálculos de uma hélice axial onde as pás poderia ter um ângulo variável entre 5° e 37°, poderia ignorar os 5 primeiros elementos (0..4). Mas se precisasse fazer a mesma coisa com uma temperatura variando entre -10°C e 30°C já complicaria (além de ter técnicas diferentes no mesmo programa).

##### GENIE

```python
// 100 doors problem
// Author: Sinuhe masan (2019)
init

	// 100 elements array of boolean type
	doors:bool[100]
    
	for var i = 1 to 100
		doors[i] = false  // set all doors closed
    
    
	for var i = 1 to 100
		j:int = i
		while j <= 100 do
			doors[j] = not doors[j]
			j = j + i
    
	print("Doors open: ")
	for var i = 1 to 100
		if doors[i]
			stdout.printf ("%d ", i)
```

Genie entrou aqui apenas para aparecer. O compilador de Vala (sintaxe parecida com C#) também compila Genie (sintaxe parecida com Python) podendo inclusive ter as duas linguagens no mesmo projeto. Provavelmente a mais fácil para trabalhar com GTK. Talvez o maior detalhe (não sei se chega a ser problema pois o Pascal também não possui) é não trabalhar com um passo diferente de 1 no loop `for`. 

##### Icon

```icon
procedure main()
    door := table(0)    # default value of entries is 0
    every pass := 1 to 100 do
        every door[i := pass to 100 by pass] := 1 - door[i]
    every write("Door ", i := 1 to 100, " is ", if door[i] = 1 then "open" else "closed")
end
```

Icon é baseada em SNOBOL e serviu de inspiração para os geradores de Python. Veja que no segundo passo, a inteiração ficou diretamente dentro das chaves servindo de índice para a tabela eliminando a fase`door[i] := 1 - door[i]`. Para quem conhece um pouco a linguagem é tão legível como `(x|y) > (3|4)` que pode ser expandida para `x > 3 or y > 3 or x > 4 or y > 4` ou para expressões como `5 > x > 3`, `every write(fatorial(2|3|6|9|66))`. 

##### JAVASCRIPT

```javascript
var doors=[];
for (var i=0;i<100;i++)
    doors[i]=false;
for (var i=1;i<=100;i++)
    for (var i2=i-1;i2<100;i2+=i)
        doors[i2]=!doors[i2];
for (var i=1;i<=100;i++)
    console.log("Door %d is %s",i,doors[i-1]?"open":"closed")
```

Programa normal sendo que o último `for` é desnecessário e, mais uma vez vemos o *workaround* para uma array onde o primeiro elemento é zero.

Mas...

```javascript
(function (n) {
    "use strict";
    function finalDoors(n) {
        var lstRange = range(1, n);
        return lstRange
            .reduce(function (a, _, k) {
                var m = k + 1;
                return a.map(function (x, i) {
                    var j = i + 1;
                    return [j, j % m ? x[1] : !x[1]];
                });
            }, zip(
                lstRange,
                replicate(n, false)
            ));
    };
    function zip(xs, ys) {
        return xs.length === ys.length ? (
            xs.map(function (x, i) {
                return [x, ys[i]];
            })
        ) : undefined;
    }
    function replicate(n, a) {
        var v = [a],
            o = [];
        if (n < 1) return o;
        while (n > 1) {
            if (n & 1) o = o.concat(v);
            n >>= 1;
            v = v.concat(v);
        }
        return o.concat(v);
    }
    function range(m, n, delta) {
        var d = delta || 1,
            blnUp = n > m,
            lng = Math.floor((blnUp ? n - m : m - n) / d) + 1,
            a = Array(lng),
            i = lng;
        if (blnUp)
            while (i--) a[i] = (d * i) + m;
        else
            while (i--) a[i] = m - (d * i);
        return a;
    }
    return finalDoors(n)
        .filter(function (tuple) {
            return tuple[1];
        })
        .map(function (tuple) {
            return {
                door: tuple[0],
                open: tuple[1]
            };
        });

})(100);
```

serve mais para provar que você é o cara. É o cara que gosta de complicar coisa simples.

##### Lua

```lua
local is_open = {}

for pass = 1,100 do
    for door = pass,100,pass do
        is_open[door] = not is_open[door]
    end
end

for i,v in next,is_open do
    print ('Door '..i..':',v and 'open' or 'close')
end
```

A linguagem é clara o suficiente para a tarefa e o autor não inventou. Apenas se baseou em outro escrevendo o último `for` supérfluo.

##### MUMPS

```lua
doors	new door,pass
	For door=1:1:100 Set door(door)=0
	For pass=1:1:100 For door=pass:pass:100 Set door(door)='door(door)
	For door=1:1:100 If door(door) Write !,"Door",$j(door,4)," is open"
	Write !,"All other doors are closed."
	Quit
```

Para quem conhece um pouquinho também é simples. Note que a construção do loop é bem parecido com Lua com a diferença que a separação é por dois pontos em vez de vírgula e a ordem é `inicio:incremento:fim` e não `inicio,fim,incremento` com em lua. As variáveis são um caso especial em  M, isto é `door` não é a mesma coisa que `door(door)` e as variáveis são tratadas como uma árvore binária. Basicamente um array esparso, isto é, você pode ter `door(6)` sem a necessidade de ter outros índices. Outro detalhe é que uma linha como `F door=1:1:100 S door(door)=0`também é legível já que as palavras reservadas podem ser abreviadas para até uma letra (do tempo em que a memória era cara e tinha que ser racionada).

##### NIM

```nim
from strutils import `%`

const numDoors = 100
var doors: array[1..numDoors, bool]

for pass in 1..numDoors:
  for door in countup(pass, numDoors, pass):
    doors[door] = not doors[door]

for door in 1..numDoors:
  echo "Door $1 is $2." % [$door, if doors[door]: "open" else: "closed"]
```

Nim é quase um Python compilado. A única coisa que é mais feia é o segundo loop onde o iterador é dado por uma função.

##### PASCAL

```pascal
var
   doors : Array[1..100] of Boolean;
   i, j	 : Integer;
   
begin
    for i := 1 to 100 do begin
        j := i;
        while j <= 100 do begin
            doors[j] := not doors[j];
            j := j + i
        end;
        if doors[i] then
            writeln(i, ' Open');
    end;
end.


```

Fiz algumas alterações na versão original. Aqui também o problema é que o passo do `for` é sempre 1 ou -1 para `downto` o que obriga mais codificação para a utilização do `while`. A caraterística é que é uma das poucas linguagens onde os arrays são arbitrários, isto é, o ínice inicial pode ser o que o programador desejar tipo `Array[-10..10]`.

##### PYTHON

```python
doors = [False] * 100
for i in range(100):
   for j in range(i, 100, i+1):
       doors[j] = not doors[j]
   print("Door %d:" % (i+1), 'open' if doors[i] else 'close')
```

Aqui o problema são os arrays iniciando em zero. 

##### RUBY

```ruby
doors = Array.new(101,0)
print "Open doors "
(1..100).step(){ |i|
(i..100).step(i) { |d|
    doors[d] = doors[d]^= 1
    if i == d and doors[d] == 1 then
      print "#{i} "
    end
  }
}
```

Arrays também iniciam em zero mas o programador optou por perder o primeiro elemento em nome da clareza. Mas...

```ruby
class Door
  attr_reader :state

  def initialize
    @state = :closed
  end
  
  def close
    @state = :closed
  end

  def open
    @state = :open
  end
  
  def closed?
    @state == :closed
  end
  
  def open?
    @state == :open
  end
  
  def toggle
    if closed? then open else close end
  end
  
  def to_s
    @state.to_s
  end
end

doors = Array.new(100) { Door.new }
1.upto(100) do |multiplier|
  doors.each_with_index do |door, i|
    door.toggle if (i + 1) % multiplier == 0
  end
end

doors.each_with_index { |door, i| puts "Door #{i+1} is #{door}." }
```

nem precisava existir. O fato da linguagem ser OO não significa que seja obrigado a usar classes para tudo.
