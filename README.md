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

## O Básico do Git

### Imagens, Não Diferenças

A principal diferença do Git para os outros VCSs é a forma como ele trata seus dados. Conceitualmente, a maioria dos outros sistemas armazenam a informação como uma lista de mudanças nos arquivos. Estes sistemas tratam a informação como um conjunto de arquivos e as mudanças feitas em cada arquivo ao longo do tempo.

![](./imgs/Armazenando%20dados%20como%20alterações%20em%20uma%20versão%20básica%20de%20cada%20arquivo..png)

Já o Git trata seus dados mais como um conjunto de imagens de um sistema de arquivos em miniatura. Toda vez que eu fizer um *commit*, ou salvar o estado do meu projeto no Git, ele basicamente tira uma foto de todos os arquivos e e armazena uma referência para esse conjunto de arquivos. Se os arquivos não forem alterados, o Git não armazena o arquivo novamente, apenas um link para o arquivo idêntico ao anterior já armazenado. O Git trata seus dados mais como um **fluxo do estado dos arquivos.**

![](./imgs/Armazenando%20dados%20como%20um%20estado%20do%20conjunto%20de%20arquivos%20do%20projeto%20ao%20longo%20do%20tempo..png)

Esta é um diferença importante entre o Git e quase todos os outros VCSs. Isso faz com que o Git seja mais como um mini sistema de arquivos com algumas ferramentas incrivelmente poderosas, ao invés de simplesmente um VCS.

### Quase Todas as Operações são Locais

A maioria das operações no Git só precisa de arquivos e recursos locais para operar. E isso faz com que o Git seja muito rápido, pois como tenho toda a história do projeto está ali mesmo no disco local, a maioria das operações parecem quase instântaneas.

Por exemplo, para pesquisar o hitórico do projeto, o Git não precisa sair para o servidor para obter a história e exibi-la - ele simplismente lê diretamente do banco de dados local. Isto significa que dá pra ver o histórico do projeto quase que instantaneamente. Se eu quiser ver as alterações feitas entre a versão atual de um arquivo e o arquivo de um mês atrás, o Git pode procurar o arquivo de um mês atrás e fazer um cálculo da diferença local, em vez de ter que pedir a um servidor remoto para faze-lo ou puxar uma versão mais antiga do arquivo do servidor remoto para fazê-lo localmente.

Isto também significa que há muito pouco que você não pode fazer se não tiver internet.

### Git tem Integridade

Tudo no Git passa por uma soma de verificações(*checksum*) antes de ser armazenado e é referenciado por esse *checksum*. Isto significa que é impossível mudar o conteúdo de qualquer arquivo ou pasta sem que o Git saiba. Esta funcionalidade está incorporada no Git nos níveis mais baixos e é parte integrante de sua filosofia. Eu não vou perder nenhuma informação durante a transferência e não vou receber um arquivo corrompido sem que o Git seja capaz de detectar.

O mecanismo que o Git utiliza para esta soma de verificação é chamado um *hash* SHA-1. Esta é uma sequência de 40 caracteres composta de caracteres hexadecimais e é calculada com base no conteúdo de uma estrutura de arquivo ou diretório no Git.

Uma *hash* SHA-1 é algo como o seguinte:
```
24b9da6552252987aa493b52f8696cd6d3b00373
```

O Git armazena tudo em seu banco de dados não pelo nome do arquivo, mas pelo valor de *hash* do seu conteúdo.