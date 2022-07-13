# Aprendendo Git

## Sobre Controle de Versão

**Controle de Versão**: é um sistema que registra alterações em um arquivo ou conjunto de arquivos ao longo do tempo para que você possa lembrar versões específicas mais tarde.

Usar um sistema de controle de versão (**VCS**) nos permite reverter para um estado anterior determinado arquivo ou um projeto inteiro, comparar as mudanças ao longo do tempo, ver quem modificou pela última vez, quando, e muito mais. Além disso, caso estraguemos tudo ou se tivermos perdido algum arquivo, podemos recupera-lo facilmente.

### Sistemas Locais de Controle de Versão

Um dos metódos de controle de versão mais populares e facéis que conhecemos é criar um diretório de backup e copiar os arquivos para esse diretório, porém apesar de ser simples, é incrivelmente propenso a erros. É fácil se esquecer em qual diretório você está e acabar sobreescrevendo o arquivo errado ou copiar algo que não tinha intenção.

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

A maioria das operações no Git só precisa de arquivos e recursos locais para operar. E isso faz com que o Git seja muito rápido, pois como tenho toda a história do projeto ali mesmo no disco local, a maioria das operações parecem quase instântaneas.

Por exemplo, para pesquisar o histórico do projeto, o Git não precisa sair para o servidor para obter a história e exibi-la - ele simplismente lê diretamente do banco de dados local. Isto significa que dá pra ver o histórico do projeto quase que instantaneamente. Se eu quiser ver as alterações feitas entre a versão atual de um arquivo e o arquivo de um mês atrás, o Git pode procurar o arquivo de um mês atrás e fazer um cálculo da diferença local, em vez de ter que pedir a um servidor remoto para faze-lo ou puxar uma versão mais antiga do arquivo do servidor remoto para fazê-lo localmente.

Isto também significa que há muito pouco que você não pode fazer se não tiver internet.

### Git tem Integridade

Tudo no Git passa por uma soma de verificações (*checksum*) antes de ser armazenado e é referenciado por esse *checksum*. Isto significa que é impossível mudar o conteúdo de qualquer arquivo ou pasta sem que o Git saiba. Esta funcionalidade está incorporada no Git nos níveis mais baixos e é parte integrante de sua filosofia. Eu não vou perder nenhuma informação durante a transferência e não vou receber um arquivo corrompido sem que o Git seja capaz de detectar.

O mecanismo que o Git utiliza para esta soma de verificação é chamado um *hash* SHA-1. Esta é uma sequência de 40 caracteres composta de caracteres hexadecimais e é calculada com base no conteúdo de uma estrutura de arquivo ou diretório no Git.

Uma *hash* SHA-1 é algo como o seguinte:
```
24b9da6552252987aa493b52f8696cd6d3b00373
```

O Git armazena tudo em seu banco de dados não pelo nome do arquivo, mas pelo valor de *hash* do seu conteúdo.

### Os Três Estados (Muito Importante!)

O Git tem três estados principais que os arquivos podem estar: **commited, modificado (modified)** e o **preparado (staged)**.

- *Commited*: significa que os dados estão armazenados de forma segura no banco de dados local.
- *Modified*: significa que o arquivo foi alterado, mas ainda não foi feito o commit no banco de dados.
- *Staged*: significa que foi marcada a versão atual de um arquivo modificado para fazer parte do próximo commit.

E com isso temos as três seçoes principais de um projeto Git: **o diretório Git**, **o diretório de trabalho** e **área de preparo**.

- *Git directory*: é onde o Git armazena os metadados e o banco de dados de objetos do projeto. Esta é a parte mais importante do Git, e é o que é copiado quando um repositório é clonado de outro computador.
- *Working Directory*: é uma simples cópia de uma versão do projeto. Esses arquivos são pegos do banco de dados compactado no diretório Git e colocados no disco para ser usado ou modificado.
- *Staging Area*: é um arquivo, geralmente contido no diretório Git, que armazena informações sobre o que vai entrar no próximo commit. É por vezes referido como "índice", mas também é comum referir-se a ele como área de preparo.

![](./imgs/Principais%20seções%20de%20um%20projeto%20Git.png)

**Fluxo de Trabalho básico do Git**:
1. Você modifica arquivos no seu diretório de trabalho.
2. Você prepara os arquivos, adicionando imagens deles à sua área de preparo.
3. Você faz commit, o que leva os arquivos como eles estão na área de preparo e armazena essas imagens de forma permanente no diretório do Git.
   
Se uma versão específica de um arquivo está no diretório Git, é considerado *commited*. Se for modificado, mas foi adicionado à área de preparo, é considerado *preparado*. E se ele for alterado depois de ter sido carregado, mas não foi preparado, ele é considerado *modificado*.

## Instalação do Git

### Instalando no Linux

Se eu quiser instalar o Git através de um instalador binário, eu posso usar a ferramenta básica de gerenciamento de pacotes que vem com a minha distribuição.

Para ver como instalar em outras distribuições é só ir em http://git-scm.com/download/linux

Para distribuições baseadas no **Debian** como o Ubuntu, deve se usar o `apt-get`:
```
   $ sudo apt-get install git-all
```

## Configuração Inicial do Git

É preciso fazer essas configurações apenas uma vez por computador e os efeitos vão se manter após as atualizações. E se eu quiser mudar alguma configuração futuramente, basta rodar os mesmos comandos.

O Git vem com uma ferramenta chamada `git config` que permite ver e atribuir variáveis de configuração que controlam todos os aspectos de como o Git aparece e opera. Estas variáveis podem ser armazenadas em três lugares diferentes:

1. `/etc/gitconfig`: válido para todos os usuários no sistema e todos os seus repositórios. Se eu passar a opção `--system` para `git config`, ele lê e escreve neste arquivo.
2. `~/.gitconfig` ou `~/.config/git/config`: Somente para o meu usuário. Posso fazer o Git ler e escrever neste arquivo passando a opção `--global`.
3. `config` no diretório Git (ou seja, `.git/config`) de qualquer repositório que eu esteja usando: específico para este repositório.

Cada nível sobrescreve os valores no nível anterior, ou seja, valores em `.git/config` prevalecem sobre `/etc/git/config`.

No Windows, o Git procura pelo arquivo `.gitconfig` no diretório `$HOME` (`C:\Users\$USER` para a maioria). Ele também procura por `/etc/gitconfig`, mesmo sendo relativo à raiz do sistema que é onde quer que você tenha instalado Git no seu sistema.

### Sua Identidade

A primeira coisa que deve ser feita ao instalar o Git é configurar o nome de usuário e endereço de email. Isto é importante porque cada commit usa essa informação, e ela é carimbada de forma imutável nos commits que serão feitos.

Como configurar nome de usuário e email:
```
$ git config --global user.name "Fulano de Tal"
$ git config --global user.email fulanodetal@exemplo.br
```
> **Note**
> 
> Se quiser substituir o nome para um projeto específico, basta rodar o comando sem a opção `--global` dentro daquele projeto.

### Seu Editor

É possível escolher o editor de texto padrão que vai ser chamado quando o Git precisar que entre com uma mensagem. Se não for configurado, o Git usará o editor padrão, que normalmente é o Vim (no linux).

Para escolher um editor de texto diferente, como o Emacs, é só fazer o seguinte:
```
$ git config --global core.editor emacs
```
### Testando suas Configurações

Para listar as configurações do Git, basta rodar o comando:
```
$ git config --list
user.name=Fulano de Tal
user.email=fulanodetal@exemplo.br
color.status=auto
color.branch=auto
color.interactive=auto
color.diff=auto
...
```

Podemos ver também o que tem em uma configuração específica usando `git config <key>`:
```
$ git config user.name
Fulano de Tal
```
## Pedindo Ajuda

Se precisarmos de ajuda, há três formas de acessar a página de manual de ajuda (manpage) para qualquer um dos comandos Git:
```
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```
Por exemplo, dá para ver a manpage do comando *config* rodando:
```
$ git help config
```
> **Note**
> 
> Estes comandos podem ser acessados de qualquer lugar, mesmo desconectado. Caso seja necessária ajuda específica, basta tentar nos canais #git ou #github no servidor *IRC Freenode (irc.frenode.net)*.

## Obtendo um Repositório Git

É possível obter um repositório Git de duas maneiras:  
   1 - Pegar um repositório local que atualmente não está sobre controle de versão e transformá-lo em um repositório Git.
   
   2 - Fazer um clone de um repositório Git existente em outro lugar.

### Inicializando um repositório em um Diretório Existente

Para começar a fazer o controle de versão de um projeto existente com Git, é preciso ir para o diretório do projeto e usar o comando init:

```
$ git init
```

Isso cria um subdiretório chamado `.git` que contém todos os arquivos necessários do repositório - um esqueleto de repositório Git. 

Para que um arquivo passe a ser monitorado, devemos usar o comando `git add` para especificar qual é o arquivo e logo depois usar o comando `git commit`.

```
$ git add *.c 
$ git add LICENSE
$ git commit -m "inicial project version"
```
E pronto um repositório Git está criado.

### Clonando um Repositório Existente

Para clonar um repositório Git existente, devmos usar o comando `git clone`. E com isso o Git recebe uma cópia completa de praticamente todos os dados que o servidor possui. Cada versão de cada arquivo no histórico do projeto é obtida por padrão quando o `git clone` é executado. Isso se torna muito importando no caso do servidor ter sido corrompido por exemplo, pois basta usar qualquer uma das cópias de qualquer um dos clientes para reverter o servidor ao estado em que estava quando foi clonado (talvez algumas configurações do servidor sejam perdidas, mas todos os dados versionados estarão lá.)

Exemplo de como clonar um repositório:
```
$ git clone [url]
$ git clone https://github.com/libgit2/libgit2
```

Isso cria um diretório chamado **libgit2**, inicializa um diretório **.git** dentro dele, recebe todos os dados deste repositório e deixa disponível para o trabalho a cópia da última versão. Se entrarmos no novo diretório **libgit2**, veremos que todos os arquivos do projeto nele, pronto para serem editados ou utilizados. 

Caso seja do nosso desejo alterar o nome do repósitorio de destino, basta adicionar o nome desejado ao lado da URL:
```
$ git clone [url] nome
$ git clone https://github.com/libgit2/libgit2 mylibgit
```
O Git possui diversos protocolos de transferencia que podem ser utilizados. O exemplo anterior usa o protocolo **https://**, mas também pode ser encontrado **git://** ou **user@server:path/to/repo.git** que usam o protocolo de transferência SSH.

## Gravando Alterações no repositório

Cada arquivo em um diretório de trabalho pode estar em um dos seguintes estados: **rastreado** e **não-rastreado**. 

Arquivos rastreados são arquivos que foram incluidos no último snapshot (commit); eles podem ser não modificados, modificados ou preparados (adicionado ao stage). Em resumo, **arquivos rastreados são os arquivos que o Git conhece.**

Arquivos não rastreados são todos os outros - aqueles arquivos do diretório que não foram incluidos no último snapshot (commit) e não estão na área de stage. Quando um repositório é clonado pela primeira vez, todos os arquivos desse repositório serão rastreados e não modificados já que o Git acabou de obtê-los e você ainda não editou nada.

Assim que algum arquivo é editado, Git o consdidera modificado, porque ele foi editado desde o último commit. Você prepara os arquivos editados e então faz o commit das suas alterações, e o ciclo se repete.

![](./imgs/Ciclo%20de%20vida%20do%20status%20dos%20arquivos.png)

### Verificando os Status dos Arquivos

A principal ferramenta para saber o estado dos arquivos é o comando `git status`. Se esse comando for executado imediatamente após clonar um repositório, vai ser visto algo assim:

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

Isso signifca que nenhum dos arquivos rastreados foi modificado. O Git também não está vendo nenhum arquivo não rastreado, senão eles estariam listados ali. O comando também informa qual é a branch atual e se ela não divergiu com a branch do servidor. A branch do servidor por padrão vai ser sempre a "main" (antiga master).

Exemplo: adicionar um README simples que não existia antes e por isso não é rastrado, e ver o que o `git status` vai reportar.
```
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)
  README
nothing added to commit but untracked files present (use "git add" to track
```

É possível ver que o novo arquivo README é um arquivo não rastreado, vejamos que ele foi listado como **untracked files** no status. O Git não vai passar a incluir o arquivo nos commits a não ser que isso seja feito explicitamente. O comportamento do Git é dessa forma para evitar que arquivo binários gerados automaticamente ou outros arquivos que não são para serem incluídos sejam acidentalmente incluídos.

### Rastreando Arquivos Novos

Para começar a rastrear um novo arquivo, é preciso usar o comando `git add`.

```
$ git add nome
$ git add README
```

Depois de adicionado, ao executar o comandos `git status` podemos ver que agora o arquivo está sendo rastreado e preparado (staged) para o commit:

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file: README
```

É possível saber que o arquivo está preparado porque ele aparece sob o título "Changes to be committed". Caso seja feito o commit neste momento, a versão do arquivo que existia no instante que `git add` foi executado é a que será armazenada no histório de *snapshots*. 

>**Note**: O comando `git add` recebe o caminho de um arquivo ou de um diretório. Se for um diretório, o comando adiciona todos os arquivos contidos nesse diretório recursivamente.

### Preparando Arquivos Modificados

Ao modificar um arquivo já rastreado, e então executar `git status`, algo assim deve aparecer:

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file: README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified: CONTRIBUTING.md
```

O arquivo **CONTRIBUTTING.md** aparece sob a seção "Changes not staged for commit" -- que indica que um arquivo rastreado foi modificado no diretório de trabalho mas ainda não foi mandado para o stage (preparado). Para mandá-lo para o stage, é preciso executar o comando `git add`. O `git add` é um comando de múltiplos propósitos: serve para começar a rastrear arquivos e também para outras coisas, como marcar arquivos que estão em conflito de mesclagem como resolvidos. Pode ser útil pensar nesse comando mais como "adicione este conteúdo ao próximo commit". Executando `git add CONTRIBUITTING.md` e logo depois `git status`:

```
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file: README
  modified: CONTRIBUTING.md
```
Ambos os arquivos estão preparados (no stage) e entrarão no próximo commit. Neste momento suponha que você lembrou que precisa fazer uma mudança no CONTRIBUTTING.md antes de fazer o commit. Você abre o arquivo, faz a mudança e está pronto para fazer o commit. No entanto, vamos executar `git status` mais uma vez:

```
$ vim CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file: README
  modified: CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified: CONTRIBUTING.md
```

Agora o **CONTRIBUTTING.md** está listado como preparado (staged) e também como não preparado (unstage). Acontece que o Git põe um arquivo no stage exatamente como ele está no momento em que o comando `git add` é executado. Se `git commit` for executado agora, a versão do **CONTRIBUTTING.md** que vai para o repositório é aquela que foi adicionada com o `git add`, não a versão que está no diretório de trabalho. Se um arquivo for modificado depois de executar `git add` é necessário executar o comando novamente para que a versão mais recente seja adicionada ao stage:

```
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file: README
  modified: CONTRIBUTING.md
```

### Status Curto

O `git status` é bem completo, mas também bastante verboso. O Git também tem uma flag para status curto, que permite ver as alterações de forma mais compacta. essa flag é o comando `git status -s` ou `git status --short`:

```
$ git status -s
 M README
MM Rakefile
A lib/git.rb
M lib/simplegit.rb
?? LICENSE.txt
```

Siginificado das letras e símbolos ao lado do nome dos arquivos:
- `??`: Arquivos novos que não estão sendo rastreados.
- `A`: Novos arquivos que foram adicionados a área de stage.
- ` M`: arquivos modificados e que não foram para o stage ainda.
- `M`: arquivos modificados que foram para o stage.
- `MM`: arquivos modificado que foram para o stage e foram modificado novamente.
  
### Ignorando Arquivos

Frequentemente teremos uma classe de arquivos que não queremos que sejam adicionados automaticamente pelo Git e nem mesmo que ele os mostre como não rastreado. Geralmente, esses arquivos são gerados automaticamente, tais como arquivos de log ou arquivos produzidos pelo sistema de compilação (build). Nesses casos, é possível criar um arquivo chamado `.gitignore`, contendo uma lista de padrões de nomes de arquivo que devem ser ignorados.

Exemplo de `.gitignore`:
```
$ cat .gitignore
*.[oa]
*~
```
A primeira linha diz ao Git para ignorar arquivos que terminem com ".o" ou ".a" -- arquivos objeto ou de arquivamento, que podem ser produtos do processo de compilção. A segunda linha diz ao Git para ignorar todos os arquivos cujo nome termine com um til (~), que é usado por muitos editores de texto como o Emacs, para marcar arquivos temporários. Também é possível incluir diretórios **log, tmp** ou **pid**; documentação gerada automaticamente; e assim por diante. Configurar um arquivo `.gitignore`, antes de começar um repositório geralmente é uma boa ideia para que não sejam incluidos arquivos indesejados no repositório Git.

As regras para os padrões que podem ser usados no arquivo `.gitignore` são as seguintes:
- Linhas em branco ou começando com `#` são ignoradas.
- Os padrões que normalmente são usados para nomes de arquivos funcionam.
- Você pode iniciar padrões com uma barra (`/`) para evitar recursividade.
- Você pode terminar padrões com uma barra (`/`) para especificar um diretório.
- Você pode negar um padrão ao fazê-lo iniciar com um ponto de exclamação (`!`).
  
Padrões de nome de arquivo são como expressões regulares simplificadas usadas em ambiente *shell*:
- (`*`): casa com zero ou mais caracteres.
- `[abc]`: casa com qualquer caracter dentro dos colchetes (neste caso, a,b ou c).
- (`?`): casa com um único caracter qualquer.
- (`[0-9]`): casam com qualquer caracter entre eles (neste caso, de 0 a 9).
- (`**`): casa com diretórios aninhados.
- (`a/**/z`): casa com `a/z, a/b/z, a/b/c/z`, e assim por diante.

Outro exemplo de arquivo `.gitignore`:
```
# ignorar arquivos com extensão .a
*.a

# mas rastrear o arquivo lib.a, mesmo que você esteja ignorando os arquivos .a acima
!lib.a

# ignorar o arquivo TODO apenas no diretório atual, mas não em subdir/TODO
/TODO

# ignorar todos os arquivos no diretório build/
build/

# ignorar doc/notes.txt, mas não doc/server/arch.txt
doc/*.txt

# ignorar todos os arquivos .pdf no diretório doc/
doc/**/*.pdf
```

>**Note**: O Github mantém uma lista com exemplos de arquivo `.gitignore`: https://github.com/github/gitignore

>**Note**: Em casos simples, um repositório deve ter um único arquivo `.gitignore` em seu diretório raiz, o qual é aplicado recursivamente a todos o repositório. Contudo também é possível ter arquivos `.gitignore` adicionais em subdiretórios. As regras definidas nesses `.gitignore` internos se aplicam somente aos arquivos contidos no diretório em que estão localizados. veja `man gitignore` para mais informações.

### Visualizando as Alterações dentro e fora do Stage

Caso queiramos saber exatamente o que foi alterado, não apenas quais arquivos, é possível usar o comando `git diff`. Ele é útil para respodender duas questões: 1 - O que foi alterado, mas ainda não foi mandado para o stage? 2 - E o que está no stage, pronto para o commit? Apesar de o `git status` respoder a essas perguntas de forma genérica, listando os nomes dos arquivos, o `git diff` exibe exatamente as linhas que foram adicionadas e removidas -- o **patch**, como costumava se chamar.  

Por exemplo, se alteramos o **README** e o mandar para o stage e então alterar o **CONTRIBUTTING.md** sem mandá-lo para o stage. Se o `git status` for executado veremos:
```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  
  modified: README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified: CONTRIBUTING.md

```

O `git diff` nos mostrará algo do tipo:
```
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

Esse comando compara o que está no meu diretório de trabalho com o que está no stage. O resultado permite que saber quais alterações eu fiz que ainda não foram mandadas para o stage.

Se eu quiser ver as alterações que eu já mandei para o stage e que vão estar no próximo snapshot, basta usar o `git diff --staged`. Este comando compara as alterações que estão no stage com meu último commit:

```
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

Se todas as alterações já tiverem sido enviadas para o stage e eu usar o `git diff` sem a flag `--staged`, a saída do `git diff` vai ser vazia, esse comando não mostra as alterações feitas desde o último commit, apenas as alterações que ainda não foram preparadas.

O `git diff --staged` e o `git diff --cached` são sinônimos.

>**Note**: Para ver as diferenças com uma ferramenta gráfica ou um programa externo, eu possi executar `git difftool` e ai é possível ver qualquer uma das diferenças em um software como **emerge, vimdiff** e muitos outros. Executar `git difftool --tool-help` para ver o que há disponível no meu sistema.

### Fazendo Commit das alterações

O jeito mais simples de fazer commit é:
```
$ git commit
```
Fazendo isso, vai ser aberto o editor que foi configurado como padrão.

>**Note**: O editor é determinado pela variável de ambiente **EDITOR**.

o editor mostra a seguinte mensagem de texto (este é um exemplo da tela do Vim):
```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
# new file: README
# modified: CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```
A mensagem de commit padrão contém a saída mais recente do comando `git status`, comentada, e uma linha em branco no topo. Eu posso remover esses comentários e digitar a mensagem que eu quero ou deixar para ajudar a lembrar o que faz parte do commit.

>**Note**: Para um lembrete ainda mais explícito do que foi alterado. podemos passar `-v` para o `git commit`. Isso inclui as diferenças (diff) da alteração no editor, para que possamos ver exatamente quais alterações estão entrando no commit.

Quando sair do editor, o Git cria o commit com essa mensagem (com os comentários e diferenças removidos).

Alternativamente, podemos digitar a mensagem do commit diretamente na linha de comando, depois da opção `-m` do comando `commit`:
```
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```
Assim o commit está pronto! A saída informa algumas informações: em qual branch foi feito o commit (**master**), o checksum SHA-1 (**463dc4f**), quantos arquivos foram alterados e estatísticas sobre o número de linhas adicionadas e removidas.

Lembre-se de que o commit grava o snapshot que você deixou na área de stage. Qualquer alteração que você não tiver mandado para o stage permanecerá como estava, em seu lugar; você pode executar outro commit para adicioná-la ao seu histórico. Toda vez que você executa um commit,
você está gravando um snapshot do seu projeto que você pode usar posteriormente para fazer comparações, ou mesmo restaurá-lo.

### Pulando a Área de Stage

Mesmo sendo muito útil para preparar os commits, a área de stage é um pouco mais complexa do que o necessário. Para pular a área de stage, o Git fornece um atalho simples. A opção `-a`, do comando `git commit`, faz o Git mandar todos arquivos rastreados para o stage automaticamente, antes de fazer o commit, permitindo pular o `git add`:
```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified: CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)

```
A opção `-a` inclui todos os arquivos alterados. É importante ter cuidado ao usar isso, pois as vezes alguns arquivos indesejados podem ser adicionados.

### Removendo Arquivos

Para remover um arquivo do Git, é preciso removê-lo dos arquivos rastreados (mais precisamente, removê-lo da área de stage) e então fazer um commit. O comando `git rm` faz isso, e também remove o arquivo do diretório de trabalho para que o arquivo não apareça com um arquivo não rastreado nas próximas interações.

Se simplesmente só removermos o arquivo do diretório, ele aparecerá sob a área "Changes not staged for commit" (isto é, fora do stage) da saída do `git status`:
```
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  deleted: PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

Mas ao executar `git rm`, o arquivo vai ser preparado para remoção (retirado do stage):
```
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  deleted: PROJECTS.md
```
Então no próximo commit que for feito, o arquivo vai ser eliminado e não será mais rastreado. Se o arquivo tiver sido alterado ou se já tiver adicionado à área de stage, será necessário forçar a remoção com a opção `-f`. Essa é uma medida de segurança para previnir a exclusão acidental de dados que ainda não tenham sido gravados em um snapshot e que não poderão ser recuperados do histórico.

Outra coisa útil que o `git rm` pode fazer é quando quisermos manter um arquivo no disco local, mas que ele não seja mais rastreado pelo Git. Isso é particularmente útil se não tiver sido adicionado no .gitignore e acidentalmente mandado para o stage, como um grande arquivo de log ou um monte de arquivos compilados `.a`. Para fazer isso, use a opção `--cached`:
```
$ git rm --cached README
```
É possível passar arquivos, diretórios e padrões de nomes para o comando `git rm`. Coisas como:
```
$ git rm log/\*.log
```
A barra invertida na frente do `*` é necessária porque o Git faz sua própria expansão de nomes de arquivos em adição a que é feita pelo shell. Esse comando remove todos os arquivos que tenham a extensão `.log` do diretório `log/`.

Ou, fazer algo como o seguinte:
```
$ git rm \*~
```
Esse comando remove todos os arquivos cujos nomes terminem com um `~`.

### Movendo Arquivos

Diferente de outros VCS's, o Git não rastreia explicitamente movimentação de arquivos. Se eu renomear um arquivo no Git, ele não armazena metadados indicando que determinado arquivo foi renomeado. Pórem, o Git é bastante esperto para perceber isso depois do fato ocorrido.

É possível renomear o arquivo usando um comando do Git:
```
$ git mv arq_origem arq_destino
```
Exemplo: 
```
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  renamed: README.md -> README

```

No entanto, isso é equivalente a executar:
```
$ mv README.md README
$ git rm README.md
$ git add README
```
O Git percebe que, implicitamente, se trata de um arquivo renomeado, de maneira que não importa o jeito que o arquivo foi renomeado. A única diferença real é que `git mv` é um comando em vez de três. Mais importante, eu posso usar qualquer ferramenta pra renomear o arquivo e cuidar do `add/rm` depois, antes de fazer o commit.

## Vendo o histórico de Commits

Depois de ter criado vários commits ou se eu tiver clonado um repositório com um histórico de commits pré-existente, é possível ver esse histórico. O comando para isso é `git log`.

Exemplo de saida do `git log`:
```
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date: Mon Mar 17 21:52:11 2008 -0700

  changed the version number

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date: Sat Mar 15 16:40:33 2008 -0700

  removed unnecessary test

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date: Sat Mar 15 10:31:28 2008 -0700

  first commit
```
Por padrão, sem argumentos, `git log` lista os commits feitos no repositório em ordem cronológica inversa; O commit mais recente aparece primeiro. Esse comando mostra lista de cada commit com o checksum SHA-1, o nome e email do autor, data de inserção e a mensagem do commit. 

Variações do `git log` que podem ser usadas:
- `git log -p`: mostra as diferenças introduzidas em cada commit.
- `git log -2`: lista no retorno apenas os últimos 2 commits.
- `git log --stat`: mostra algumas estatísticas abreviadas para cada commit. Lista de arquivos modificados, quantos arquivos foram modificados, e quantas linhas nestes arquivos foram adicionadas ou removidas.
- `git log --pretty=oneline`: mostra cada commit em única linha. é possível usar o valor **short, full e fuller** no lugar do **oneline**.
- `git log --pretty=format:"%h - %an, %ar : %s"`: permite especificar o formato que eu quero de retorno.
  - Exemplo:
    ```
      $ git log --pretty=format:"%h - %an, %ar : %s"
      ca82a6d - Scott Chacon, 6 years ago : changed the version number
      085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
      a11bef0 - Scott Chacon, 6 years ago : first commit
    ```
  - Lista com opções úteis para o `git log --pretty=format`:
     ![](./imgs/Opções%20úteis%20para%20o%20git%20logo%20pretty=format.png)  