# PicoLisp

Eu já tinha escutado mas nunca tinha parado para ver o PicoLisp. Essa semana parei na página e li algumas coisas que me interessaram.

- *PicoLisp is quite different from other Lisps. The main reason is its focus on data.*
  
- *In programming, the ultimate purpose is to manipulate data, not functions. Code and data are equivalent in Lisp, so you get the function manipulations for free anyway, but the focus should be on data.*
  
- *PicoLisp has one single internal data type: The "cell".*
  
- *On the language level it has only three data types: Numbers, symbols and cons pairs. They are all built from cells.*
  

É interpretado. YMMV mas aqui o executável do PicoLisp tem 363K . Um DU -h no diretório lib retorna 636K (achei curioso os números 363 - 636).

Para baixar e maiores informações, visite a página do [PicoLisp](https://picolisp.com/wiki/?home)

#### REPL

Para iniciar o REPL digite `pil +`. O mais no final é para iniciar no mode de depuração. Ele carrega automaticamente algumas bibliotecas que serão úteis. Um exemplo é a possibilidade de ver a documentação usando `(doc 'ssl)`, por exemplo. É necessário ter o [w3m](https://w3m.sourceforge.net/) instalado.

Para sair do REPL entre com `(bye)`. Pode parecer frescura mas sempre é interessante saber como sair de um ambiente.

#### Exemplo de introdução

Uma olhada no [twitter](https://twitter.com/PicoLispREPL) e me deparei com um exemplo que poderia ser interessante:

```lisp
(de elevation (City)
   (ssl "en.wikipedia.org" (pack "wiki/" City)
      (and (from "Elevation") (from "infobox-data\">") (till "&" T)) ) )

: (elevation "Berlin")
-> "34"

: (elevation "Lhasa")
-> "3,656"
```

As três primeiras linhas definem a função que, passando o nome de uma cidade, lê a página na wikipedia e retorna a altitude (em relação ao nível do mar). Bem, até imagino como funciona mas, em programação, imaginar não é o caminho correto. Ou sabe ou não sabe.

- `(de elevation (City)`

Então `de` inicia a definição da função, `elevation` é o nome (símbolo) pelo qual a função será chamada e `City` a variável que receberá o parâmetro. Em PicoLisp, variáveis iniciam com letra maiúscula (ou com asterisco se forem globais).

- `(ssl "en.wikipedia.org" (pack "wiki/" City)`

Aqui já é um pouco mais complicado. O protótipo é `(ssl 'host 'path . prg)`. Irá ler o conteúdo da página indicada por *host + path* e executar *prg* tendo como entrada o resultado da página lida. Se for passado *Porto_Alegre*, o `pack` irá concatenar `wiki/` com a cidade. O resultado final será: `ssl "en.wikipedia.org" "wiki/Porto_Alegre"`.

- `(and (from "Elevation") (from "infobox-data\">") (till "&" T)) ) )`

O `and` avalia todas as expressões, da esquerda para a direita. Se alguma for avaliada como `NIL` retorna imediatamente. Caso contrário irá retornar o valor da última expressão.

O `(from` irá posicionar o ponteiro logo após a palavra informada. Caso não encontre, retorna `NIL` é tudo termina.

O `(till 'any` retorna o conteúdo na posição da entrada atual até encontrar o caractere `any` ou até o final da entrada se for `NIL`. O `T` no final indica para retornar uma string e não uma lista de caracteres.

Poderíamos salvar o exemplo em um arquivo para rodar como um script. O resultado ficaria assim:

```lisp
#!/usr/bin/picolisp /usr/lib/picolisp/lib.l
(load "@lib/misc.l")
(de elevation (City)
    (ssl "en.wikipedia.org" (pack "wiki/" City)
         (and (from "Elevation") (from "infobox-data\">") (till "&" T))))

(prin
  (elevation (opt))
  "^J")

(bye)
```

- A primeira linha é necessária para executar diretamente o arquivo após um `chmod +x`.
  
- A segunda linha `(load "@lib/misc.l")` é necessária pois o PicoLisp não foi inicializado no modo de depuração, então as bibliotecas não foram carregadas.
  
- `(opt)` contém o conteúdo do parâmetro para passar para a função *elevation*. Existem outros truques para a linha de comando, mas fica para uma próxima.
  
- PicoLisp não é Common Lisp. Format não funcionou para tirar as aspas do resultado. Então usei o `prin` e coloquei no final `"^J"` para ir para a próxima linha.
  

#### Web

PicoLisp é bem rico na área. Um exemplo:

```lisp
(allowed () "!foo")
(load "@lib/http.l" "@lib/xhtml.l")

(de foo ()
   (html 0 "Title" NIL NIL
      (<h1> NIL "Hello World!") ) )

(server 8080 "!foo")
```

Supondo que esteja no arquivo `picoServer.l`, basta chamar `pil picoServer.l` e abrir o navegador em `localhost:8080` para ter uma página com `Hello World!`.

Antes de se aventurar, é necessário ler a documentação com calma. Pela natureza da linguagem, se sua página tiver um campo para entrada de dados, existe a possibilidade de script injection entre outros detalhes.

Se você executar o trecho:

```lisp
   (html 0 "Title" NIL NIL
      (<h1> NIL "Hello World!") ) )
```

resulta em:

```html
HTTP/1.0 200 OK
Server: PicoLisp
Date: Sat, 06 Jan 2024 04:22:34 GMT
Cache-Control: max-age=0
Cache-Control: private, no-store, no-cache
Content-Type: text/html; charset=utf-8

<!DOCTYPE html>
<html lang="en">
<head>
<meta name="viewport" content="width=device-width"/>
<title>Title</title>
</head>
<body>
<h1>Hello World!</h1>
</body>
</html>
```

É possível incluir css, js e tudo mais que seja necessário para gerar a página desejada (acho eu, pois a documentação das bibliotecas e nula ou eu não encontrei; UTSL).

Também existe a possibilidade de executar a linha de comando `pil @lib/http.l @lib/xhtml.l @lib/form.l --server 8088 +` em um diretório. Dessa forma, qualquer programa poderá ser executado como `localhost:8080/script.l`.

Os exemplos acima nem arranham a superfície.

#### BD

Ainda não cheguei aqui. Mas PicoLisp possui um BD que pode ser utilizado nos scripts. Inicialmente os registros são objetos. Existe uma linguagem SQL simplificada e `PiLog` que é baseada em Prolog para a consulta.

#### Fim

Faltou dizer que tem uma implementação para o Android e outras coisinhas.

Mas, por enquanto, fico por aqui.
