# Explicacação

Nesse projeto buscamos desenvolver algoritmos que permite que agentes expliquem suas decisões para usuários que usam o sistema.

Para isso, usamos um problema artificial como base. 

## O problema do rio

No problema do rio, um agente está em um lado da margem do rio e quer ir para o outro lado. O agente tem duas opções:
(i) nadar de qualquer ponto da margem do rio, ou (ii) ir ao longo da margem do rio até uma ponte. 

Este problema é modelado como uma grade Nx × Ny, onde x = 0 e x = Nx-1 representa a margem do rio; e y = Ny-1 representa a ponte e y = 0 representa uma cachoeira onde o agente pode ficar preso ou morrer. O objetivo fica do outro lado da margem do rio, longe do ponte, xg = Nx-1 e yg = 0. 

O agente pode se mover na direções cardinais. Se as ações forem tomadas na margem do rio ou na ponte, então as transições são determinísticas para as direções cardenais respectivas; se as ações forem tomadas no rio então as transições são probabilísticas e seguem a direção cardinal com probabilidade 1 - P_r  ou segue para baixo do rio com probabilidade P_r . A cachoeira é modelada como estados *dead-end*. O custo imediato é 1.

## Problemas de interpretabilidade

 Aqui, consideramos primeiramente eventos E(A) da forma: E(A) = {h : existe t tal que h(t) = A}. Ou seja, o conjunto de histórias em que um estado A = (x,y) foi atingido.
 
 Pergunta 1: acontece E? Resp: Probabilidade de acontecer E


 Pergunta 2: se E1, acontece E2? Resp: Probabilidade de acontecer E2 condicionado a E1


## Problemas de argumentação

Pergunta 3: porque não x?

Se x, custo acumulado vai para tanto.
Se função recompensa fosse f', então seria x.
Se função de transição fosse T', então seria x.


Pergunta 4: porque y?

Se não y, custo acumulado vai para tanto.
Se função recompensa fosse f', então não seria y.
Se função de transição fosse T', então não seria y.


