# Programando funcionalmente em Lua

Todo programador já deve ter escutado algum sobre um paradigma na qual desconhecia.... bem isto é perfeitamente normal, alias a quantidade de formas de pensar (isto é o formalismo logico) é gigantesca. Vamos considerar um exemplo simples em Lua definido de forma imperativa que costuma ser a mais impregada:

```lua
for i=12, 0, -1 print(i) do end
```

Esta é uma forma simples de resolver um problema(no caso imprimir de 0 a 12), mas imagine que este mesmo problema possa ser resolvido de uma outra forma um pouco mais elegante.

```lua
y = (function (x, f_) return f_(x) or (x == 0 or y(x-1, f_)) end) y(12, print)
y(12, print)
```
Mas como isso é possivel?
Assim como o paradgima imperativo é possivel deduzir outras formas logicas que contém uma relação de isomorfismo com qualquer outro tipo de formalismo logico Turing completeness. Isto é, não importa se o seu codigo fosse executado em uma Maquina de Turing ou em uma Maquina baseada no modelo funcional é possivel "traduzir" para uma forma compreensivel á maquina seja imperativo <-> procedural <-> logico <-> funcional.

# Mas porque programação funcional

É uma boa pergunta, bem... programação funcional foi extensivamente ultlizado para trabalhos de inteligencia artifical durante a decada de 80/90, entretanto teve uma queda em seu uso devido uma difuculdade em compreender sua complexidade sobretudo em otimizações. O tempo passou é hoje ela é ultlizado justamente para otimização em compiladores modernos (LLVM, LuaJit entre outros).
