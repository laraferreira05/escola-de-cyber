Caçador.md.

**Introdução**


No desafio "Caçador de bandeiras" da PicoCTF, o objetivo era conseguir encontrar a flag através de um trecho de código que não era exibido por padrão, ou seja, **o usuário deveria descobrir como forçar o programa a mostrar esse trecho.**

 <p>
Para acessar o desafio  <a href="https://play.picoctf.org/practice/challenge/472">clique aqui;</a>
</p>

**Análise inicial**


Ao iniciar a instância do desafio, foi possível baixar o código e verificá-lo. Analisando o código, foi possível encontrar a deixa que mostrava que o programa pulava o trecho secret_intro onde estava a flag, e iniciava direto no [VERSE1]. Isso foi essencial para encontrar uma forma de fazer com que esse código mostrasse o trecho oculto onde se obtinha a bandeira.

**Solução** 


Primeiro, rodei o código no terminal como mostrava no desafio e, então, percebi a oportunidade de manipular o código na parte em que ele me pedia para digitar algo após o Crowd.

<img src="crowd.png" alt= .  >


Para que o programa me mostrasse o trecho secret_intro, digitei um conjunto de letras, que pode ser qualquer coisa, seguido de **; RETURN 0**.

**Mas por que esse comando?**

O ponto e vírgula **;** serve para separar comandos. Ele faz com que o programa execute o que veio antes (o conjunto de letras, que foi ignorado) e, em seguida, execute o comando **RETURN 0**. Este comando é usado para forçar o programa a pular para a linha **0** (o início do código), e não para o [VERSE1]. Com isso, obtivemos nossa flag.

<img src="resultado crow.png" alt= .  >



**Conclusão**


Neste desafio, aprendemos um pouco mais sobre análise de código, além de encontrarmos a deixa para resolvê-lo e obter a flag.