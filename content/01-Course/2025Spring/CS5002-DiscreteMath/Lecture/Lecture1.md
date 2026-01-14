---
date: 2025-01-06
tags:
  - neu/CS5002
weight: 1
---
## Conclusion

| Numbering System      | Key Attribute                  |
| --------------------- | ------------------------------ |
| Binary (base 2)       | more efficiency                |
| Decimal (base 10)     | easy to recognize (10 fingers) |
| Hexadecimal (base 16) | easy to converse               |

## Convert Decimal to Binary
### Conversion Method 1
$121=120+2^0$
	$=60\times2^1+2^0$
	$=30\times2\times2+2^0$
	$=15\times2^1\times2^2+2^0$
	$=(14+1)\times2^3+2^0$
	$=7\times2\times2^3+2^3+2^0$
	$=(6+1)\times2^4+2^3+2^0$
	$=6\times2^4+2^4+2^3+2^0$
	$=3\times2\times2^4+2^4+2^3+2^0$
	$=3\times2^5+2^4+2^3+2^0$
	$=(2+1)*2^5+2^4+2^3+2^0$
	$=2^6+2^5+2^4+2^3+2^0$
	$=1\times2^6+1\times2^5+1\times2^4+1\times2^3+0\times2^2+0\times2^1+1\times2^0$
	`1111001`
### Conversion Method 2
$121÷2=60 \;remainder\;1$ 
$60÷2=30 \;remainder\;0$
$30÷2=15 \;remainder\;0$
$15÷2=7 \;remainder\;1$
$7÷2=3 \;remainder\;1$
$3÷2=1 \;remainder\;1$
$1÷2=0 \;remainder\;1$
`1111001`

## Hexadecimal(base 16)
Goal come up with a system that allow us to read a simplified version of binary numbers (to not have to look at 64 0s) Turns out base 16 is great.
- each hexadecimal present 4 binary digitals

| Decimal | Binary | Hexadecimal |
| :-----: | :----: | :---------: |
|    0    |  0000  |      0      |
|    1    |  0001  |      1      |
|    2    |  0010  |      2      |
|    3    |  0011  |      3      |
|    4    |  0100  |      4      |
|    5    |  0101  |      5      |
|    6    |  0110  |      6      |
|    7    |  0111  |      7      |
|    8    |  1000  |      8      |
|    9    |  1001  |      9      |
|   10    |  1010  |      A      |
|   11    |  1011  |      B      |
|   12    |  1100  |      C      |
|   13    |  1101  |      D      |
|   14    |  1110  |      E      |
|   15    |  1111  |      F      |
### Binary convert to Hexadecimal
<u>1011</u> <u>0101</u> = B 5
<u>0010</u> <u>0110 </u> <u>1110</u> = 2 6 E


Practice:
- $39_{10}=(100111)_{2}$

---
BACK to [[Content{CS5002}.canvas|Content{CS5002}]]