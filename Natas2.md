Natas2.md
O site Natas [clique aqui para acessar](https://overthewire.org/wargames/natas/natas0.html),disponibiliza diversos desafios de segurança web (Wargames). A cada nível, o usuário deve encontrar a senha para o próximo estágio. O nome de usuário segue o padrão natas + número do nível (ex: natas0), e a dificuldade aumenta progressivamente, exigindo que as habilidades do jogador vão se aprimorando, este documento registra a resolução apartir do natas 9;


*Natas 9 --> Natas 10*

Após o login, a página apresenta uma barra de pesquisa para encontrar palavras em um dicionário. O código fonte PHP foi fornecido para análise.

<img width="882" height="766" alt="image" src="https://github.com/user-attachments/assets/a551764c-e0d2-4bb6-9e7f-e14da4fdc3ed" />


Ao ler o código, notei que a aplicação utiliza a função passthru para executar o comando grep no sistema operacional, inserindo diretamente o texto digitado na busca ($key). Isso caracteriza uma vulnerabilidade de injeção de comando. Como não há sanitização, o sistema interpreta caracteres especiais do terminal Linux como instruções, e não como texto.
Para explorar essa falha, utilizei a sintaxe de encadeamento de comandos do Linux:

; (Ponto e vírgula): Funciona como um separador, permitindo executar um segundo comando logo após o término do primeiro (grep).

cat: O comando padrão para ler e exibir o conteúdo de arquivos.

Caminho do Arquivo: /etc/natas_webpass/natas10 (padrão dos desafios anteriores).

Com a lógica pensada inseri o seguinte comando na barra de pesquisa: ; cat /etc/natas_webpass/natas10

O servidor tentou executar a busca original, mas logo em seguida executou o comando injetado, exibindo a senha do Natas 10 na tela.
<img width="607" height="208" alt="image" src="https://github.com/user-attachments/assets/b91cae4b-ea80-4a2a-aba3-bde81d2478f6" />


senha nivel 10: t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu

*Natas 10 --> Natas 11*

Semelhante ao nível anterior, temos uma barra de pesquisa. No entanto, ao analisar o código fonte, notei uma nova camada de segurança: uma "blacklist" de caracteres. O código agora filtra caracteres como o ;(ponto e virgula) usado anteriormente.Essa filtragem impede a Injeção de Comandos direta (não posso usar ; para rodar um cat ou ls). Porém, a função vulnerável ainda é passthru("grep -i $key dictionary.txt").O comando grep funciona da seguinte forma grep [O_QUE_BUSCAR] [ARQUIVO_ONDE_BUSCAR]. Como controlamos o parâmetro $key (o que buscar), podemos injetar espaços e adicionar um segundo arquivo para o grep ler.
<img width="547" height="203" alt="image" src="https://github.com/user-attachments/assets/b23a4b3e-8ffd-4bdb-81ca-a9c1c0fb9f79" />


Com isso construí o payload para manipular os argumentos do grep:

.* : Em Expressões Regulares, o ponto significa "qualquer caractere" e o asterisco "qualquer quantidade", ou seja, estou mandando o grep buscar "qualquer coisa" (para garantir que ele encontre o conteúdo da senha).

/etc/natas_webpass/natas11 : Adicionei o caminho do arquivo de senha como um segundo argumento.

"#": Utilizei a hashtag para comentar o resto da linha original. Isso faz com que o grep ignore o arquivo dictionary.txt que estava fixo no código.

Payload Final: .* /etc/natas_webpass/natas11 #

O comando executado no servidor virou: grep -i .* /etc/natas_webpass/natas11 # dictionary.txt. O grep buscou "tudo" dentro do arquivo de senha e exibiu o resultado na tela.

senha 11: UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk

*Natas 11 --> Natas 12*

A página oferece uma funcionalidade para alterar a cor de fundo (padrão #ffffff) e informa que "Cookies são protegidos com criptografia XOR". Analisando o código fonte, identifiquei o array de dados padrão: array("showpassword"=>"no", "bgcolor"=>"#ffffff"). Também capturei o cookie atual: HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GIjEJAyIxTRg%3D (onde %3D representa o caractere =). A vulnerabilidade reside na previsibilidade da criptografia XOR. A lógica matemática é comutativa: se TextoOriginal XOR Chave = Cookie, então Cookie XOR TextoOriginal = Chave. Como eu tenho o cookie criptografado e sei qual é o texto original, posso descobrir a chave secreta.

Para descobrir a chave utilizei o CyberChef. No input, colei o cookie decodificado (URL Decode). Apliquei From Base64 e depois a operação XOR. No campo "Key" do XOR, inseri o JSON original: {"showpassword":"no","bgcolor":"#ffffff"}.Obtive uma string repetitiva eDWo. 

<img width="1197" height="601" alt="natas 11 cyber chef" src="https://github.com/user-attachments/assets/aa268c6d-4190-4952-9a99-9df6f3c54b1c" />


Depois, fiz o processo inverso para criar um cookie falso.O novo JSON desejado: {"showpassword":"yes","bgcolor":"#ffffff"}. Apliquei XOR (usando a chave descoberta eDWo) e depois To Base64.
Voltei ao navegador, abri as Ferramentas de Desenvolvedor e substituí o valor do cookie data pelo novo valor gerado. Ao recarregar a página, o sistema descriptografou meu cookie, leu showpassword: yes e me entregou a senha.

senha natas 12:yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB

*Natas 12 --> Natas 13*

O nível apresenta um formulário para upload de arquivos JPEG (imagem), com tamanho máximo de 1KB.Analisando o código fonte, percebi uma vulnerabilidade crítica na função de upload. O servidor confia em um parâmetro oculto chamado filename para definir a extensão do arquivo salvo, sem validar adequadamente o conteúdo do arquivo. Isso permite um ataque de Upload de Arquivo Irrestrito, onde posso enviar um script PHP malicioso  disfarçado, mas forçar o servidor a salvá-lo com a extensão .phpCom isso criei um arquivo local contendo o seguinte código PHP:

        <?php 
                passthru('cat /etc/natas_webpass/natas13'); 
        ?>

A função passthru executa comandos diretos no terminal Linux do servidor. O comando cat lê o conteúdo do arquivo onde a senha está armazenada (/etc/natas_webpass/natas13).Selecionei meu arquivo .php no botão de upload.Antes de enviar, inspecionei o código da página e localizei o campo oculto:

        "input type="hidden" name="filename" value="aleatorio.jpg"."

Editei manualmente o atributo value, alterando a extensão de .jpg para .php. Ao clicar em "Upload File", o servidor salvou meu arquivo como um script PHP executável. O site gerou um link para o arquivo enviado. Ao clicar no link, o servidor executou o código injetado e exibiu a senha do Natas 13.

Senha 13:trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC

*Natas 13 --> Natas 14*

Um novo pedido foi feito como mostra a imagem abaixo:

<img width="1395" height="511" alt="natas 13" src="https://github.com/user-attachments/assets/2ae42902-7c78-4413-8816-685f99d33d04" />


Analisando o código PHP, notei que foi implementada uma verificação de segurança (exif_imagetype), diferentemente do nível anterior. Porém, essa função verifica apenas o cabeçalho (início) do arquivo. Isso sugere a necessidade de um ataque do tipo 'Arquivo Camaleão': um arquivo que se apresenta como JPEG no início para enganar o validador, mas contém código PHP malicioso no corpo.

Para construir o payload, utilizei o editor online ("https://hexed.it/").Inseri na primeira linha os Magic Bytes correspondentes a um arquivo JPEG (FF D8 FF E0). Ao ler esse início, o servidor valida o arquivo como uma imagem segura. Logo em seguida, inseri o payload PHP 
  "<?php
                passthru('cat /etc/natas_webpass/natas13');
        ?>" direcionado ao arquivo de senha do natas14(o mesmo usado anteriormente).
        
 Antes de realizar o upload, precisei manipular o formulário para contornar duas restrições: a extensão do arquivo e o limite de tamanho. Utilizei o Console do navegador para injetar comandos JavaScript diretamente no DOM:

document.querySelector('input[name="filename"]').value = "hack.php";
        --Este comando alterou a extensão final para .php, garantindo a execução do código.
        
document.querySelector('input[name="MAX_FILE_SIZE"]').value = "10000";
        --Este comando aumentou o limite de upload (originalmente 1000 bytes) para permitir o envio do meu arquivo gerado pelo editor hexadecimal.
        
Após executar os comandos, fiz o upload. O servidor aceitou o arquivo (enganado pelo cabeçalho JPEG) e o salvou como script PHP (devido à manipulação no console). Ao acessar o link gerado, o navegador exibiu a senha do Natas 14 misturada aos caracteres binários da imagem falsa.

senha Natas14: z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ

*Natas 14 --> Natas 15*

Ao acessar o nível, deparei-me com um formulário de login solicitando usuário e senha. O desafio também disponibilizava o código fonte para análise.
De início, analisei o código e identifiquei que a vulnerabilidade estava na construção da variável $query.

O objetivo do código era consultar o banco de dados para verificar as credenciais. Percebi que era necessário inserir uma lógica que manipulasse a consulta original, fazendo com que o banco de dados aceitasse a entrada como verdadeira, independentemente da senha.
Para explorar isso, construí meu payload iniciando com uma aspa dupla ("). Isso foi necessário porque, ao analisar o código, notei que a entrada do usuário era inserida dentro de aspas. Ao colocar uma aspa no início, eu fechei a string de texto original, permitindo que o restante do meu input fosse interpretado como comando SQL.
Em seguida, utilizei o operador OR seguido de 1=1. Isso criou uma tautologia (uma verdade absoluta), fazendo com que a condição de login fosse sempre válida. Por fim, utilizei o caractere #, que funciona como um comentário no SQL, servindo para ignorar (filtrar) o restante do código original que verificava a senha.

O payload final ficou: " OR 1=1 #. Inseri este comando no campo de usuário e coloquei qualquer valor aleatório na senha.

<img width="783" height="302" alt="image" src="https://github.com/user-attachments/assets/4badb6a8-36b5-4c76-af63-3de59edd2bb0" />

 O ataque funcionou e obtive acesso à senha do Natas 15.
 <img width="892" height="182" alt="image" src="https://github.com/user-attachments/assets/459e8764-c673-4ff4-88c0-1ea7a0e9dffd" />


senha natas 15: SdqIqBsFcz3yotlNYErZSZwblkm0lrvx

A conclusão central é que a segurança de uma aplicação começa onde termina a confiança no usuário. Os desafios de 9 a 15 demonstraram que a ausência de filtros rígidos, transforma formulários inofensivos em armas contra o servidor, permitindo desde o roubo de dados até o controle total do sistema. A regra de ouro aprendida é: 

        tratar todo dado externo como potencialmente malicioso é a única defesa eficaz contra a exploração de vulnerabilidades


