# NaN = ON ERROR RESUME NEXT

Não pretendo discutir se ovo tem ou não cabelo. Apenas mostrar alguns detelhes de que, considerar algo não numérico ou que não pode ser representado por um número como um número não é interessante para a programação e pode trazer efeitos indesejados. Na realidade, pretendo mostrar um efeito catastrófico que fica mais fácil para assimilar.

### Novo automóvel

A empresa está desenvolvendo um carro super-ultra avançado que irá, sem a intervenção do motorista, efetuar frenagem para não colidir com um obstáculo evitando acidentes. Um item de segurança que você não poderá mais viver sem. Existem centenas de sensores no carro que passam informações para o programa tomar a decisão de salvar vidas e evitar danos materiais.

### Lógica

Vamos desenvolver um sistema com as seguintes características:

- Calcular distância segura
  
  - velocidade / raizquadrada dos dados do sensor **nan** - distância + 4.8 (sensor nan é conjunto de sensores discretos desenvolvidos especialmente para o veículo que retorna um valor entre 1 e 5 baseados em inúmeros fatores como velocidade do vento, qualidade do asfalto, calibragem dos peneus e mais uma centena de itens)
    
- Se a distancia segura for menor ou igual a distancia do objeto, o sistema aciona o freio do carro para tentar miniizar a colisão e emite sinal sonoro para o motorista (se ele não viu o objeto, pode estar distraído).
  

### Programa

Poderia ser em qualquer linguage que considere NaN = Number mas vamos escolher Rust que é uma linguagem segura para dar mais confiabilidade. Um candidato para o código poderia ser:

```rust
fn distancia_segura() -> f64 {
	velocidade() / f64::sqrt(sensor_nan()) - distancia() + 4.8
}

fn iniciar_frenagem() {
	println!("Iniciar frenagem");
	println!("Alertar motorista");	
}

fn main() {	
    if distancia_segura() <= distancia() {
		iniciar_frenagem();
	}
	println!("Viagem segura!");	
}
```

Não foi detectada nenhuma falha após testes com diversas combinações de velocidades, distâncias do objeto e valores do sensor **nan**. Pronto para colocar o programa no sistema do automóvel.

### Passeio de uma família feliz

Um casal com três filhos entre 3 anos e 6 meses, um gato, um papagaio e um hamster (não confundir com Rammstein) resolveu comprar o carro pois as crianças distraiam o motorista nas viagens. Foi todo mundo para o carro, pegaram a estrada e, como a velocidade máxima era de 110 km/h, colocaram o piloto automático em 105 km/h (carro sem piloto automático não é moderno). Todos alegres dentro do carro, o pai olhando para trás fazendo bilu bilu para o filho de 6 meses, a mãe apartando a briga dos outros dois mas, como o carro era seguro, tudo tranquilo.

Só que o hamster roeu um dos sensores e o gato mijou em outro. Os dois era utilizados pelo sensor **nan** que começou a retornar -1.

Na estrada havia uma retroescavadeira que deu uma pane elétrica e parou no meio da pista. No carro, que estava a 104,7 km/h e a 5 m de distância da retroescavadeira .........

### Oooops

Como assim a mais de 100 km/h e apenas 5 m de distância? O carro é seguro. O sensor **nan**? As crianças? O gato? O hamster? O casal? Vai acontecer uma tragédia. O que houve? O que houve? O que houve? O que houve!!!!!?????

### Voltando um pouco no tempo

Como os animais estragaram o sensor **nan** e ninguém previu a possibilidade de retornar valores negativos aliados ao fato da linguagem trabalhar com NaN sem acusar erro, teremos um problema. Vejamos como se comprtaria a linguagem:

```rust
fn distancia() -> f64 {
	5.0 // lida do sensor
}

fn velocidade() -> f64 {
	104.7 // lida do sensor
}

fn sensor_nan() -> f64 {
	-1.0 // um sensor que deveria retornar 1 .. 5 deu problema
}

fn distancia_segura() -> f64 {
	velocidade() / f64::sqrt(sensor_nan()) - distancia() + 4.8
}

fn iniciar_frenagem() {
	println!("Iniciar frenagem");
	println!("Alertar motorista");	
}

fn main() {	
    println!("Distância  = {} m",distancia());
    println!("Valocidade = {} km/h",velocidade());
    println!("Dist.Seg.  = {} km/h",distancia_segura());
    if distancia_segura() <= distancia() {
		iniciar_frenagem();
	}
	println!("Viagem segura!");	
}
```

Executando o programa (você pode executar o código no [playground](https://play.rust-lang.org/) do Rust) temos:

```bash
Distância  = 5 m
Valocidade = 104.7 km/h
Dist.Seg.  = NaN km/h
Viagem segura!
```

- Como o resultado do sensor **nan** foi negativo o resultado de ```f64::sqrt(sensor_nan())```foi NaN.
  
- Os cálculos efetuados com NaN não geraram erro e retornaram NaN.
  
- A função ```distancia_segura()``` que deveria retornar um Float retornou um NaN.
  
- A expressão ```if distancia_segura() <= distancia()``` sempre irá retornar False pois NaN é um número que não é comparável com um número e retorna NaN.
  

Se a linguagem gerasse uma exceção no cálculo da raiz de número negativo como algumas que geram `DomainError` o programa iria abortar e, algum programa supervisor poderia alertar o motorista de uma falha no sistema. Da mesma forma, gerar um erro no retorno da função pois o programador queria explicitamente um Float e veio um NaN (um Float que não existe).

Me lembra claramente a instrução do BASIC ```ON ERROR RESUME NEXT``` que servia para que o programa continuasse executando a próxima instrução em caso de erro. Pelo menos era uma opção do programador. Não tenho ideia se existe uma chave para desligar a propagação automática do NaN .

### Resultado

Primeira página do jornal, Cinco pessoas mortas ou feridas em tragédia com o novo carro super seguro.

### Conclusão

Sim, NaN pode ser considerado prejudicial. Para uma linguagem inteligente, $\sqrt{-1} = 1i$ . Ou retorna um erro do tipo ```Ei, você está trabalhando com a linguagem "tal" e não com LISP```. Sim, LISP retorna um número complexo e não um NaN.

Como se não bastasse, a propagação através de funções pode causar uma boa dor de cabeça para encontrar onde está o erro.

Apesar de ter sido uma brincadeira (de mau gosto) é um fato que poderia acontecer na vida real. Existem sistema para suporte à vida, que controlam carros e aviões entre outros. Até mesmo um refrigerador que poderia ficar ligado sempre causando um consumo excessivo de energia ou desligado estragando os alimentos.
