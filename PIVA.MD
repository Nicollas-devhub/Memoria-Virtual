# Analista de incidentes:

Para esse incidente, a estratégia que será usada é a paginação por demanda, consiste em que o sistema operacional carregue para a memória física apenas as páginas que o programa está exigindo naquele momento. No incidente, está ocorrendo o descompasso de recursos, onde estão tentando utilizar um software de renderização de texturas em 4k que exija o quadruplo de memória RAM que o servidor contém. Então, graças à paginação por demanda, quando iniciar os processos o sistema operacional não irá exigir que os 128 GB sejam colocados na memória física de uma vez.


