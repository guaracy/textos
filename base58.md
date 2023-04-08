# Base 58 em Nim

### Introdução

Surgiu uma discussão sobre base 58 em um grupo de Nim. Bem, base é base. A única base que não faz sentido é uma base menor do que dois. Em uma base *n* serão utilizados *n* símbolos diferentes para a representação de cada dígito. Na base binária temos o *0* e *1* e assim vai, até uma base qualquer. Utilizamos a base 10 e, para isso, utilizamos os símbolos de *0..9*. Na base hexadecimal, convencionou-se adicional as letras *A..F* para os símbolos que faltavam. Para a base 58, utilizamos 58 símbolos diferentes. São usados os números *0..9*, as letras maiúsculas *A..Z* e as letras minúsculas *a..z* mas alguns caracteres que causam confusão foram eliminados. A letra maiúscula **O** e o número zero **0**, a letra i maiúsculo **I** e a letras l (*ele*) minúsculo **l**. Ok, agora é só agir como outra base qualquer. Ops. Não é bem assim. Algumas entidades resolveram complicar as coisas. 

|                                                                        |                                                              |
| ---------------------------------------------------------------------- | ------------------------------------------------------------ |
| [Flickr](https://www.flickr.com/groups/api/discuss/72157616713786392/) | `123456789abcdefghijkmnopqrstuvwxyzABCDEFGHJKLMNPQRSTUVWXYZ` |
| Bitcoin                                                                | `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz` |
| [Ripple](https://en.wikipedia.org/wiki/Ripple_(payment_protocol))       | `rpshnaf39wBUDNEGHJKLM4PQRST7VWXYZ2bcdeCg65jkm8oFqi1tuvAxyz` |
| GMP                                                                    | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuv` |

A ordem dos símbolos é essencial. Não podemos trocar o `5`pelo `7` e obtermos o mesmo resultado. Nos três o valor de `A` é diferente. Então é necessário saber para quem será feita a conversão. Pessoalmente acho o Bitcoin mais correto.



```nim
import strutils, bignum

# Bitcoin
let Chars = "123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz"

#
proc decodeBase58(s:string):string=
  var v = newInt(0)
  for ch in s:
    v = v * 58 + Chars.find(ch)
  while v > 0:
      result.insert $(v mod 256).toInt.char
      v = v div 256
      
#
proc encodeBase58(val:Int):string=
  var v=val
  while v > 0:
    result.add(Chars[(v mod 58).toInt])
    v = v div 58
  let h = result.high
  for i in 0..(h div 2):
    swap result[i], result[h - i]

#    
proc bytes2base58(s:string):string =
  result=encodeBase58(newInt(s.toHex, 16))

#
proc hex2base58(s:string):string =
  result=encodeBase58(newInt(s, 16))

#
when isMainModule:
  echo bytes2base58("aBc")
  echo hex2base58("614263")  # a=61h B=42h c=63h
  echo encodeBase58(newInt("6373987",10)) # 614263h = 6373987
  echo bytes2base58("Teste base 58 com Nim")
  echo decodeBase58("ZfmL")
  echo decodeBase58("YqfFr5FEEnfjkGJ4PMrUqQN8bc2KWGvTvY")


```

- `decodeBase58` : tem como entrada um valor codificado na base 58 e retorna uma string com o resultado

- `encodeBase58` : tem como entrada um *bignum* e retorna uma string com a codificação na base 58

- `bytes2base58` : entra uma string ou outra sequência de bytes e retorna uma string na base 58

- `hex2Base58` : a entrada é um número hexadecimal e também retorna uma string codificada na base 58



Por curiosidade, o resultado do programa acima é:

```bash
ZfmL
ZfmL
ZfmL
6C11q4Q23dVV3DfaozngpKJWQBYMi
aBc
O teste termina POR aqui!
```






