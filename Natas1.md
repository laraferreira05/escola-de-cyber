Natas.md

O site Natas [clique aqui para acessar](https://overthewire.org/wargames/natas/natas0.html),disponibiliza diversos desafios de segurança web (Wargames). A cada nível, o usuário deve encontrar a senha para o próximo estágio. O nome de usuário segue o padrão natas + número do nível (ex: natas0), e a dificuldade aumenta progressivamente, exigindo que as habilidades do jogador vão se aprimorando.

*Natas 0 --> Natas 1*

O desafio se inicia no Level 0, onde o próprio site fornece as credenciais iniciais:
User: natas0
Pass: natas0

Ao logar, vemos a mensagem:

        "You can find the password for the next level on this page." (Você pode encontrar a senha para o próximo nível nesta página.)

A senha não está visível no texto da tela. A técnica básica de reconhecimento web é verificar o Código Fonte. Como o navegador interpreta o HTML para mostrar a página bonita, ele esconde comentários e tags que contêm informações valiosas.
Ao inspecionar o código (pressionando Ctrl+U ou clicando com o botão direito --> "Exibir código-fonte"), encontrei a senha escondida dentro de um comentário HTML.


Senha level 1: 0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq

*Natas 1 --> Natas 2*

Ao acessar o Nível 1 com a senha obtida, deparamos com a seguinte mensagem:

        "You can find the password for the next level on this page, but rightclicking has been blocked!"
        (Você pode encontrar a senha para o próximo nível nesta página, mas clicar com o botão direito do mouse está bloqueado!)

O desenvolvedor tentou "proteger" o código fonte utilizando um script simples em JavaScript que intercepta o clique do botão direito do mouse. No entanto, isso é uma proteção apenas visual.
Para contornar isso, não dependemos do mouse. Utilizei o atalho de teclado Ctrl + U. Isso abre o visualizador de código fonte do navegador, ignorando totalmente o bloqueio de script da página, assim então conseguindo a senha ro nível 2.

https://github.com/laraferreira05/escola-de-cyber/issues/1#issue-3678035022


senha level 2: TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI 

*Natas 2 --> Natas 3*

Realizado o login, obtive apenas a mensagem:
         "There is nothing on this page" (Não há nada nesta página).

Como de costume, verifiquei o código fonte. Notei que havia uma imagem de 1x1 pixel sendo carregada na página, incorporada através da tag <img>. O atributo src da imagem apontava para: files/pixel.png.
A existência de um diretório /files/ me chamou a atenção. Tentei acessar esse diretório diretamente, removendo o nome do arquivo (pixel.png) da URL.

URL Original: .../files/pixel.png

URL Modificada: .../files/

Ao acessar o diretório, percebi que o servidor estava com a Listagem de Diretórios habilitada. Em vez de mostrar uma página em branco ou erro, ele listou todos os arquivos contidos ali.Encontrei um arquivo chamado users.txt. Ao clicar nele, visualizei a lista de usuários e senhas, contendo a credencial para o próximo nível
imagem 

Senha level 3: 3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH 

*Natas 3 --> Natas 4*

Ao realizar o login no Natas 3, novamente recebi a mensagem de que não havia nada na página. Analisando o código fonte, percebi um comentário intrigante:

        "No more information leaks! Not even Google will find it this time..." 
        (Sem mais vazamentos de informação! Nem o Google vai encontrar desta vez...)

A menção ao "Google" é uma dica clara sobre Indexadores de Busca. Os sites utilizam um arquivo padrão chamado robots.txt para dizer aos buscadores (como o Google) quais pastas eles não devem acessar ou indexar.Para investigar o que o site estava escondendo do Google, adicionei /robots.txt ao final da URL principal e recebi o seguinte conteudo:
imagem
A linha Disallow: /s3cr3t/ indica que existe uma pasta secreta chamada s3cr3t que o administrador não quer que seja encontrada. Voltei à URL principal e acessei esse diretório manualmente obtendo então a senha ao próximo nível.


Para entender mais sobre robots.txt [clique aqui]("https://www.google.com/aclk?sa=L&ai=DChsSEwjM8bWf6OqQAxXkaEgAHdwEH_oYACICCAEQABoCY2U&co=1&ase=2&gclid=CjwKCAiA2svIBhB-EiwARWDPjpT7zgg6DuUls5ZtTOUEeppUIGYiHXdx0Se_5NDTSe-ynITtbgo5ZRoCEkcQAvD_BwE&cid=CAASlwHkaBC5zDn-PERzVFl6uLz_j0Vc9XOcFZkilhiRZI1nIQ26ECtdowSy4nDrG2yYiCb5pZ0qBuccV5gKxnS9_sgbvNFNTkuhG2dovd6poEIci3xklFn8lXGOwfjzEypLUPiFE3hKbScyc72kr0MatLMCZX8OeLfTrhkEQA8O1DVdFC5C4MXv9ebin7z51VgYedrD72oPn87Y&cce=2&category=acrcp_v1_32&sig=AOD64_3ipw9yVYoig49x4Wx9CU__5-85zQ&q&nis=4&adurl&ved=2ahUKEwjt86-f6OqQAxWUL7kGHQ1XCskQ0Qx6BAgREAE")

senha level 4: QryZXc2e0zahULdHrtHxzyYkj59kUxLQ

*Natas 4 --> Natas 5*

Ao acessar o nível, recebi a mensagem de erro:

        "Access disallowed. You are visiting from "http://natas4..." 
        while authorized users should come only from "http://natas5..."

Essa mensagem indica que o servidor está verificando a origem da minha requisição. No protocolo HTTP, existe um cabeçalho chamado Referer, que diz ao servidor de qual site você veio antes de chegar ali. O desafio exige que o servidor "pense" que eu cliquei num link vindo do Natas 5.
Para forjar essa informação, utilizei o Burp Suite (ferramente do kali linux).Interceptulei a requisição de acesso ao Natas 4.Localizei a linha:  http://natas4.natas.labs.overthewire.org/Modifiquei manualmente para: http://natas5.natas.labs.overthewire.org/. Encaminhei a requisição (Forward) assim então ao recarregar a página obtive a senha.

 senha level 5: 0n35PkggAPm2zbEpOU802c0x0Msn1ToK

*Natas 5 --> Natas 6*

Acessando o nível, a página exibia:
       
        "Access disallowed. You are not logged in" (Acesso negado. Você não está logado).

Como não havia formulário de login (usuário/senha), suspeitei que o controle de sessão fosse feito via Cookies. Cookies são pequenos arquivos de texto que o site salva no navegador para lembrar quem você é.Abri as Ferramentas de Desenvolvedor, fui na aba Application --> Cookies.Encontrei um cookie suspeito:

Nome: loggedin

Valor: 0

A lógica booleana sugere que 0 representa Falso e 1 representa Verdadeiro. O site estava apenas checando esse arquivo local para decidir se eu estava logado ou não, o que é uma falha grave de segurança.Editei o valor do cookie loggedin de 0 para 1.Recarreguei a página e obtive a senha.

 imagem 
 senha 6:0RoJwHdSKWFTYR5WuiAewauSuNaBXned

*Natas 6 --> Natas 7*

Ao acessar a página, deparei-me com um formulário solicitando um "segredo" (Input secret). O botão "View sourcecode" estava disponível.
Analisando o código PHP, notei a seguinte linha: 

        include "includes/secret.inc";

Isso indica que o script está "puxando" o conteúdo de um arquivo externo chamado secret.inc que está dentro da pasta includes. Se a senha secreta é comparada no código, ela deve estar definida dentro desse arquivo.
Como o arquivo está no servidor web, tentei acessá-lo diretamente pela URL (Barra de Endereços).

URL Original: .../index.php

URL Modificada: .../includes/secret.inc

Ao acessar esse caminho, o servidor me mostrou o conteúdo do arquivo, revelando a variável $secret. Segredo encontrado: FOEIUWGHFEEUHOFUOIU
Copiei esse código, voltei à página inicial, colei no formulário e submeti. O sistema validou o segredo e liberou a senha do próximo nível.

imagem 
imagem

senha segredp: FOEIUWGHFEEUHOFUOIU

senha 7: bmg8SvU1LizuWjx3y7xkNERkHxGre0GS

*Natas 7 --> Natas 8*

A página apresentava dois links simples: "Home" e "About". Ao clicar neles, notei que a URL mudava de forma padronizada:

index.php?page=home

index.php?page=about

Além disso, havia um comentário no código HTML:

        Hint: password for webuser natas8 is in /etc/natas_webpass/natas8

A estrutura ?page= sugere que o site utiliza o parâmetro page para decidir qual arquivo carregar e exibir na tela. Se o servidor não filtrar o que escrevemos ali, podemos obrigá-lo a ler arquivos do sistema operacional que não deveriam ser públicos.Sabendo que a senha está no caminho /etc/natas_webpass/natas8 (conforme a dica), manipulei a URL para apontar diretamente para esse arquivo em vez de "home" ou "about".

URL Modificada: http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8

Ao carregar a página, o script PHP obedeceu ao comando, foi até a pasta interna do servidor, leu o arquivo de senha e exibiu seu conteúdo na tela misturado ao HTML.
imagem

senha 8: xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q

*Natas 8 --> Natas 9*

 
 A página apresentava um campo para inserir um "segredo" (Input secret). Mais uma vez, o código fonte PHP estava disponível para análise.Ao examinar o código, encontrei a variável $encodedSecret contendo uma longa string hexadecimal (3d3d5163...). Logo abaixo, havia a função que gerou esse segredo:

        PHP
        function encodeSecret($secret) {
                return bin2hex(strrev(base64_encode($secret)));
        }

A função realiza três passos para esconder a senha:

base64_encode: Codifica em Base64.

strrev: Inverte a string resultante (String Reverse).

bin2hex: Converte tudo para hexadecimal.

Para descobrir o segredo original, precisei realizar o processo inverso (descriptografar), seguindo a ordem contrária das operações, utilizando o site [covertesrting]("https://www.convertstring.com/pt_BR/EncodeDecode/HexDecode"), obtive uma nova string "==QcCtmMml1ViV3b" .


Notei que o resultado parecia um Base64, mas os caracteres de preenchimento estavam no início, o que indicava que a string estava invertida. Utilizei o [string reverser](https://onlinestringtools.com/reverse-string) para corrigir a ordem onde me deu como resultado "b3ViV1lmMmtCcQ==".Com a string agora no formato correto, utilizei o  [decode]("https://www.base64decode.org/") para revelar o texto original chegando na senha segredo, que ao ser adicionada na página Natas me deu a senha ao próximo nível.

senha  secreta: oubWYf2kBq

senha 9: ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t

Conclui que com esses desafios iniciais do Natas consolidaram os fundamentos da exploração web. Começando pela análise de Código Fonte e Robots.txt, avancei para a exploração de vulnerabilidades críticas como Listagem de Diretóriose manipulação de Cookies e Headers HTTP.

A prática comprovou que validações feitas apenas no lado do cliente são inseguras e que a Engenharia Reversa é essencial para entender a lógica do servidor.


