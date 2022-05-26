# Aprendendo Git

## Sobre Controle de Versão

**Controle de Versão**: é um sistema que registra alterações em um arquivo ou conjunto de arquivos ao longo do tempo para que você possa lembrar versões específicas mais tarde.

Usar um sistema de controle de versão (**VCS**) nos permite reverter para um estado anterior determinado arquivo ou um projeto inteiro, comparar as mudanças ao longo do tempo, ver quem modificou pela última vez, quando, e muito mais. Além disso, caso estraguemos tudo ou se tivermos perdido algum arquivo, podemos recupera-lo facilmente.

### Sistemas Locais de Controle de Versão

Um dos metódos de controle de versão mais populares e facéis que conhecemos é criar um diretório de backup e copiar os arquivos para esse diretório, porém apesar de ser simples, é incrivelmente propensa a erros. É fácil se esquecer em qual diretório você está e acabar sobreescrevendo o arquivo errado ou copiar algo que não tinha intenção.

Para resolver esse problema, foram desenvolviddos **VCSs locais** que tem um banco de dados simples que mantêm todas as alterações nos arquivos sob controle de revisão.

![](./imgs/Controle%20de%20versão%20local.png)

Uma das ferramentas VCS mais populares foi um sistema chamado **RCS**. Ele funciona mantendo conjuntos de alterações (ou seja, as diferenças entre os arquivos) em um formato especial no disco; ele pode, em seguida, re-criar como qualquer arquivo se parecia em qualquer ponto no tempo, adicionando-se todas as alterações.

### Sistemas Centralizados de Controle de Versão

Uma das questões importantes que nós encontramos é que precisamos que seja possível colaborar com desenvolvedores em outros sistemas. Para lidar com essa questão, Sistemas Centralizados de Controle de Versão (CVCSs) foram desenvolvidos. Estes sistemas, têm um único servidor que contém todos os arquivos de controle de versão, e um número de clientes que usam esses arquivos a partir desse lugar central.

![](imgs/Controle%20de%20versão%20centralizado.png)

O controle de versão centralizado nos oferece muitas vantagens, principalmente em relação a VCSs locais:
- Temos ciência de em que ponto está cada um no projeto.
- Os administradores podem controlar quem pode fazer o que.
- Facilidade em administrar CVCS em relação a bancos locais em cada cliente.

Porém, esse tipo de controle também apresenta desvantagens:
- Se o servidor tiver um problema, enquanto esse problema não for resolvido, não é possível que ninguém trabalhe.
- Se o disco rígido do banco de dados central for corrompido, e backups não forem mantidos, tudo está perdido, exceto versões pontuais que cada colaborador possa ter em suas máquinas locais.

Sistemas VCS locais sofrem com esse mesmo problema, sempre que tudo estiver salvo apenas em um único lugar, existe o risco de ser perdido.

### Sistemas Distribuídos de Controle de Versão

Em um **DVCS** (como Git, Mercurial, Bazaar, ou Darcs), clientes não somente usam o estado mais recente dos arquivos: eles duplicam localmente o repositório completo. Assim, se qualquer servidor der pau, e esses sistemas tiverem colaborando por meio dele, qualquer um dos repositórios de clientes pode ser copiado de volta para o servidor. Cada clone é de fato um bakcup completo.

![](./imgs/Controle%20de%20versão%20distribuído.png)

Além disso, muito desses sistemas trabalham bem com vários repositórios remotos, de forma que diferentes grupos de colaboradores possam contribuir para o projeto de maneiras diferentes e configurando diferentes fluxos de trabalho que não são possíveis em sistemas centralizados, como modelos hierárquicos.