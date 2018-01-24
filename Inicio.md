# Entedendo os conceitos de programação funcional em Lua

Funções em Lua são chamadas de funções de primeira classe, isto significa que podemos passar seus valores por argumentos ou mesmo armazenar em variaveis (pratica conhecida como funções anonimas), alem disso podemos acessar variaveis fora do seu escopo.

```lua
x = 12
print((function() return x end) ())
```

Isso significa que lua contém propriedades para intepretar calculo lambda, portanto é possivel programar funcionalmente em lua.
Vamos definir a seguinte função lambda:

```lua
function(x) return (function(y) return (x+y) end) end
```

Perceba que a função recebe apenas um argumento e retorna outra função realizando a soma entre x+y. No caso ```(function(x) return (function(y) return (x+y) end) end) (12, 12)``` retornaria 24. Este processo se denomina curry, que significa dividir o numero de argumentos em apenas um e seguir com uma sequencia de funções. A partir de agora ultizaremos apenas funções com esse padrão. Em programação funcional tudo é absotulamente tudo é função isto significa que para estudarmos seu poder computacional vamos ter que abstrair algumas coisinhas considere então:

```lua
function(x) return (function(y) return (x+y) end) end
```

Sabendo disso podemos elaborar uma forma de simular o `if` de linguagens imperativas

```lua
i = function(x) return x() end
v = function(x) return v end
eq = function(x) return (function(y) return (y == x) and (function (z) return i(z) end) or (function (z) return v(z) end) end) end 

eq(2)(2)(function() print("equal") end)
```
O conceito é bem simples o combinator i retorna evaluating a função no seu unico paramentro(isto é a função sendo chamada), já v ignora seu unico paramentro e retorna ele mesmo, por fim eq verifica se x == y e retorna lambda de i se for verdadeiro ou v se for falso. Reformulando o codigo podemos tirar as variaveis para obter:

```lua
eq = function(x) return (function(y) return x == y end) end
(function (i) return (function(v) return (function(x) return (function(y) return eq(x, y) and (function (z) return i(z) end) or (function (z) return v(z) end) end) end) end) end) 
(function(x) return x() end) (function(x) return v end) (2) (2) (function() print("equal") end)
```

Deixando apenas eq (==) e abstraindo o resto fica facil perceber que não precisamos de variaveis ou de outras formas de construções isto é apenas funçôes são necessarias. Logo em seguida vamos ir mais a frente e abstrair alguns valores que precisaremos posteriomente. Definimos então church integers como:

```lua
v = function(x) return end
zero = function() return v end
_print = function() io.write("*") end
inc = function (n) return (function (y) return (function() y() return n() end) end) end
```

Fica facil então implementar o incremento de um numero que começa em 1 ```inc(zero) (_print)``` ou 2 ```inc (inc(zero) (_print)) (_print) ()```, mas como foi dito anteroimente não precisamos de funções anonimas logo refazendo o seguinte codigo fica:

```lua
_print = function() io.write("*") end

(function (n) return (function (y) return (function() y() return n() end) end) end) ((function (n) return (function (y) return (function() y() return n() end) end) end)((function() return (function(x) return end) end)) (_print)) (_print) ()
```
Que gera o output ```"**"``` equivalente a 2. Agora para conseguirmos executar algoritmos mais complexos precisamos definir uma forma de repetição. Que fica facilmente definida com uma recursividade ultilizando Short Anonymous Functions.

```lua
x = (function() print("Infinite") return x() end) x()
```

No entanto não queremos ultlizar variaveis como suporte, então ultilizaremos em nosso material algum combinator capaz de fazer lambda suportar recursividade.

```lua
--- FIXED POINT FUNC
 (function (x) return x(x) end) (function (x) print("infinite") return x(x) end)
 ```

Passando a função ```(function (x) print("infinite") return x(x) end)``` como argumento em uma função e colocando ela mesma como argumento podemos chamar ela mesma para executar uma recursividade infinita. Então agora podemos pensar em algoritmos mais complexos

```lua
_print = function() io.write("*") end
_newline = function() io.write("\n") end

(function (x) return (function (y) return x(x)(y) end) end) (function (x) return (function(y) y() _newline() return x(x)((function (n) return (function (y) return (function() y() return n() end) end) end)(y) (_print)) end) end) (function() return (function(x) return end) end)
```

Que irá imprimir :

```
*
**
***
****
*****
******
*******
********
*********
**********
***********
************
*************
**************
***************
(... infinite ...)
```
