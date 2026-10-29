# Analista de incidentes:

### Paginação por demanda

Para esse incidente, a estratégia que será usada é a paginação por demanda, consiste em que o sistema operacional carregue para a memória física apenas as páginas que o programa está exigindo naquele momento. No incidente, está ocorrendo o descompasso de recursos, onde estão tentando utilizar um software de renderização de texturas em 4k que exija o quadruplo de memória RAM que o servidor contém. Então, graças à paginação por demanda, quando iniciar os processos o sistema operacional não irá exigir que os 128 GB sejam colocados na memória física de uma vez.

### Page fault

conforme a renderização avança, ele referencia endereços virtuais cujas páginas não estão atualmente mapeadas na RAM de 32 GB. Com isso, o hardaware identifica a ausência da página e gera uma page fault. A ação do SO, força a suspender temporariamente a execução da tarefa de renderização, procurando a página solicitada no disco rígido e copiá ela para a memória RAM.

### O impacto na performance e o colapso

No contexto do problema relatado na história, observa-se a ocorrência de um fenômeno clássico do sistema operacional conhecido como thrashing (ou sobrecarga de paginação). Como a demanda de renderização exige 128 GB de espaço de endereçamento, superando em quatro vezes a capacidade da memória RAM disponível (32 GB), o sistema entra em um ciclo vicioso. Nessa dinâmica, o sistema operacional carrega uma página necessária para a RAM, mas precisa removê-la quase imediatamente para abrir espaço para a próxima textura requisitada pelo software. Com isso, a CPU passa a maior parte do seu tempo ociosa ou gastando ciclos preciosos apenas no gerenciamento de interrupções, filas de espera de disco e transferências contínuas de páginas.

Esse comportamento culmina no travamento completo da aplicação. O disco de armazenamento (SATA/NVMe) atinge seu limite físico de largura de banda, operando a 100% de uso. Como a taxa de transferência do disco é infinitamente mais lenta do que a velocidade de acesso da RAM e da CPU, o sistema perde a capacidade de progredir no processamento real dos quadros. A máquina não chega a desligar, mas entra em um estado de quase paralisação funcional, pois quase a totalidade dos recursos computacionais passa a ser consumida exclusivamente pela busca e troca de dados no disco.


# Arquiteto de Sistemas Operacionais:

## A solução do Arquiteto

A TLB acelera a tradução de endereços quando a página já está na RAM (p. 139–141). Aqui o gargalo é falta de memória física, portanto ela não resolve a vulnerabilidade. Os mecanismos que atacam o problema são:

**a) Controle de carga (p. 155).** Mesmo com o melhor algoritmo e uma boa alocação, a ultrapaginação pode ocorrer. O livro diz que a única solução real é livrar-se temporariamente de alguns processos, levando-os para o disco e liberando seus quadros para os que estão ultrapaginando. No servidor, isso significa suspender serviços concorrentes para deixar a RAM disponível ao renderizador.

**b) Monitoramento por PFF (p. 154–155).** O algoritmo de frequência de faltas de página indica quando aumentar ou reduzir a alocação de quadros de um processo, mantendo as faltas dentro de limites aceitáveis. O livro também diz que, quando o PFF aponta processos que precisam de mais memória e nenhum que precise de menos, é sinal de que só o controle de carga resolve.

**c) Algoritmo de substituição baseado no conjunto de trabalho (p. 153).** O livro aponta envelhecimento (que aproxima o LRU) e WSClock como os dois melhores na prática. O WSClock é bom e eficiente de implementar. Já o FIFO é uma má escolha, porque pode descartar páginas ainda em uso.

**d) Área de troca em disco separado (p. 164).** O livro recomenda criar a área de troca num disco separado do sistema de arquivos, para equilibrar a carga de E/S. Isso reduz a disputa entre paginação e leitura/escrita dos arquivos de textura.