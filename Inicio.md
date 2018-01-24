# Programando funcionalmente em Lua

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

Perceba que a função recebe apenas um argumento e retorna outra função realizando a soma entre x+y. No caso ```(function(x) return (function(y) return (x+y) end) end) (12, 12)``` retornaria 24. Este processo se denomina curry, que significa dividir o numero de argumentos em apenas um e seguir com uma sequencia de funções.
