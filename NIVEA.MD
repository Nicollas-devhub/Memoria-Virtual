# Arquiteto de Sistemas Operacionais:

## A solução do Arquiteto

A TLB acelera a tradução de endereços quando a página já está na RAM (p. 139–141). Aqui o gargalo é falta de memória física, portanto ela não resolve a vulnerabilidade. Os mecanismos que atacam o problema são:

**a) Controle de carga (p. 155).** Mesmo com o melhor algoritmo e uma boa alocação, a ultrapaginação pode ocorrer. O livro diz que a única solução real é livrar-se temporariamente de alguns processos, levando-os para o disco e liberando seus quadros para os que estão ultrapaginando. No servidor, isso significa suspender serviços concorrentes para deixar a RAM disponível ao renderizador.

**b) Monitoramento por PFF (p. 154–155).** O algoritmo de frequência de faltas de página indica quando aumentar ou reduzir a alocação de quadros de um processo, mantendo as faltas dentro de limites aceitáveis. O livro também diz que, quando o PFF aponta processos que precisam de mais memória e nenhum que precise de menos, é sinal de que só o controle de carga resolve.

**c) Algoritmo de substituição baseado no conjunto de trabalho (p. 153).** O livro aponta envelhecimento (que aproxima o LRU) e WSClock como os dois melhores na prática. O WSClock é bom e eficiente de implementar. Já o FIFO é uma má escolha, porque pode descartar páginas ainda em uso.

**d) Área de troca em disco separado (p. 164).** O livro recomenda criar a área de troca num disco separado do sistema de arquivos, para equilibrar a carga de E/S. Isso reduz a disputa entre paginação e leitura/escrita dos arquivos de textura.