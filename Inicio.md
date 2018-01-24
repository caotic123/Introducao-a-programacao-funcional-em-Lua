# Paradigmas de Programação

Todo programador já deve ter escutado algo sobre um paradigma na qual desconhecia.... bem isto é perfeitamente normal, aliás a quantidade de formas de pensar (isto é o formalismo lógico) é gigantesca. Vamos considerar um exemplo simples em Lua definido de forma imperativa que costuma ser a mais empregada:

```lua
for i=12, 0, -1 print(i) do end
```

Esta é uma forma simples de resolver um problema(no caso imprimir de 0 a 12), mas imagine que este mesmo problema possa ser resolvido de uma outra forma um pouco mais elegante.

```lua
y = (function (x, f_) return f_(x) or (x == 0 or y(x-1, f_)) end) y(12, print)
y(12, print)
```
Mas como isso é possível?
Assim como o paradigma imperativo é possível deduzir outras formas lógicas que contém uma relação de isomorfismo com qualquer outro tipo de formalismo lógico Turing completeness. Isto é, não importa se o seu código fosse executado em uma Maquina de Turing ou em uma Máquina baseada no modelo funcional é possível "traduzir" para uma forma compreensível à máquina seja imperativo <-> procedural <-> lógico <-> funcional.

# Mas porque programação funcional?

É uma boa pergunta, bem... programação funcional foi extensivamente utilizado para trabalhos de inteligência artificial durante a década de 80/90, entretanto teve uma queda em seu uso devido uma dificuldade em compreender sua complexidade sobretudo em otimizações. O tempo passou e hoje ela é utilizado justamente para otimização em compiladores modernos (LLVM, LuaJit entre outros).

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

(function (x) return (function (y) return x(x)(y) end) end) (function (x) return (function(y) y() _newline() return x(x)((function (n) return (function (y) return (function() y() return n() end) end) end)(y) (_print)) end) end)
(function() return (function(x) return end) end)
```

A seguinte função recebe 0 e então adiciona +1 e imprime o resultado a cada recursividade

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

Agora finalmente podemos definir listas com um simples exemplo que cria uma lista com o valor 0:

```lua
(function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) (function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) (function() return function(x) return end end)
```

Assim como scheme podemos dar um "car" em uma lista e retornar seu primeiro elemento como no caso de adicionar o elemento 0 na lista incrementar 2 vezes e depois retornar o 1 elemento da lista imprimindo ```**```:

```lua
_print = function() io.write("*") end
(function(x) return x ((function(x) return (function(y) return y end) end)) end ) (function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) 
(function (x) return (function(y) return (function(z) return z(x)(y) end) end) end)
((function (n) return (function (y) return (function() y() return n() end) end) end) ((function (n) return (function (y) return (function() y() return n() end) end) end) 
((function() return (function(x) return end) end)) (_print)) (_print))
```

Não é só possivel desenvolver o car como também outras abstrações comoo o caar que retorna o 2 elemento da lista(dada uma lista com os valores (0, 2)):

```lua
_print = function() io.write("*") end
(function(x) return (x ((function(x) return (function(y) return x end) end))) ((function(x) return (function(y) return y end) end)) end) ((function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) ((function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) (function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) ((function (n) return (function (y) return (function() y() return n() end) end) end) ((function (n) return (function (y) return (function() y() return n() end) end) end)((function() return (function(x) return end) end)) (_print)) (_print)) ) (function() return (function(x) return end) end)) ()
```

No exemplo a seguir iremos construir uma lista (1, 1, 1) e iremos percorrer e imprimir até que seu valor seja nil value parando o interpretador/compilador de Lua.

```lua
_print = function() print("*") end

(function (x) return (function (y) return x(x)(y) end) end)
(function (x) return (function(y) ((function(x) return x ((function(x) return (function(y) return y end) end)) end) (y)) ()
return (x) (x) (y (function(x) return (function(y) return x end) end)) end) end) ((function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) 
((function (x) return (function(y) return (function(z) return z(x)(y) end) end) end) ((function (x) return (function(y) return (function(z) return z(x)(y) end) end) end)
(function(n) return (function(x) return (function(y) return end) end) end) ((function (n) return (function (y) return (function() y() return n() end) end) end) (function() return function(x) return end end) 
(_print))) ((function (n) return 
(function (y) return (function() y() return n() end) end) end) (function() return function(x) return end end) (_print))) ((function (n) return (function (y) return (function() y() return n() end) end) end) (function() return function(x) return end end) (_print)))
```



