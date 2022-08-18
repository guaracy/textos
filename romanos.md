# Conversão de arábicos para romanos.

Conversão de numeros arábicos para romanos pode ser melhor entendida quando você descobre que está trabalhando na mesma base. Vamos desconsiderar se eles tinham o conceito de zero.

| Arábico | Romano |
| ------- | ------ |
| 0       | 0      |
| 1       | I      |
| 2       | II     |
| 3       | III    |
| 4       | IV     |
| 5       | V      |
| 6       | VI     |
| 7       | VII    |
| 8       | VIII   |
| 9       | IX     |

O que é o número 2457 ?

$2*10=20$

$20+4=24$

$24*10=240$

$240+5=245$

$245*10=2450$

$2450+7=2457$



Pronto. É só aplicar o mesmo processo usando numerais romanos. O maior problema pode ser a multiplicação por 10.  Mas vamos lá.

$IxX=X$ (10)

$VxX=L$ (50)

$XxX=C$ (100)

$CxX=D$ (500)

$DxX=M$ (1000)



Pronto. Você acabou de aprender a tabuada de 1 a 500  vezes 10 em apenas 5 linhas. Execute para cada repetição do algarismo romano. $VII * X = LXX$. Vamos ver:

$45 = 4 * 10 + 5$

$IV * X + V = XL + V = XLV$



Votemos ao nosso número anterior que era 2457.



$II x X = XX$ (2*10=20)

$XX + IV = XXIV$ (20+4=24)

$XXIV * X = CCXL$ (24*10=240)

$CCXL + V = CCXLV$ (240+5=245)

$CCXLV * X = MMCDL$ (245*10=2450)

$MMCDL + VII = MMCDLVII$ (2450+7=2457)



Vocẽ precisará apenas escrever uma rotina de multiplicação. Em COBOL ficou assim:

```cobol
INSPECT WS-RESULT CONVERTING "IVXLCDM" TO "XLCDM**"
```
















