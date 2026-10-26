# Analista de incidentes:

### Paginação por demanda

Para esse incidente, a estratégia que será usada é a paginação por demanda, consiste em que o sistema operacional carregue para a memória física apenas as páginas que o programa está exigindo naquele momento. No incidente, está ocorrendo o descompasso de recursos, onde estão tentando utilizar um software de renderização de texturas em 4k que exija o quadruplo de memória RAM que o servidor contém. Então, graças à paginação por demanda, quando iniciar os processos o sistema operacional não irá exigir que os 128 GB sejam colocados na memória física de uma vez.

### Page fault

conforme a renderização avança, ele referencia endereços virtuais cujas páginas não estão atualmente mapeadas na RAM de 32 GB. Com isso, o hardaware identifica a ausência da página e gera uma page fault. A ação do SO, força a suspender temporariamente a execução da tarefa de renderização, procurando a página solicitada no disco rígido e copiá ela para a memória RAM.

### O impacto na performance e o colapso

No contexto do problema relatado na história, observa-se a ocorrência de um fenômeno clássico do sistema operacional conhecido como thrashing (ou sobrecarga de paginação). Como a demanda de renderização exige 128 GB de espaço de endereçamento, superando em quatro vezes a capacidade da memória RAM disponível (32 GB), o sistema entra em um ciclo vicioso. Nessa dinâmica, o sistema operacional carrega uma página necessária para a RAM, mas precisa removê-la quase imediatamente para abrir espaço para a próxima textura requisitada pelo software. Com isso, a CPU passa a maior parte do seu tempo ociosa ou gastando ciclos preciosos apenas no gerenciamento de interrupções, filas de espera de disco e transferências contínuas de páginas.

Esse comportamento culmina no travamento completo da aplicação. O disco de armazenamento (SATA/NVMe) atinge seu limite físico de largura de banda, operando a 100% de uso. Como a taxa de transferência do disco é infinitamente mais lenta do que a velocidade de acesso da RAM e da CPU, o sistema perde a capacidade de progredir no processamento real dos quadros. A máquina não chega a desligar, mas entra em um estado de quase paralisação funcional, pois quase a totalidade dos recursos computacionais passa a ser consumida exclusivamente pela busca e troca de dados no disco.
