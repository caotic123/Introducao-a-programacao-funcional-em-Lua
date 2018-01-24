# Programando funcionalmente em Lua

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

# Mas porque programação funcional

É uma boa pergunta, bem... programação funcional foi extensivamente utilizado para trabalhos de inteligência artificial durante a década de 80/90, entretanto teve uma queda em seu uso devido uma dificuldade em compreender sua complexidade sobretudo em otimizações. O tempo passou e hoje ela é utilizado justamente para otimização em compiladores modernos (LLVM, LuaJit entre outros).
