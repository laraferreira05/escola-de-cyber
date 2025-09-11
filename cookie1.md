cookie.md

Introdução 

A  CTF "Receita Secreta do Monstro dos Biscoitos" ,desafio do picoCTF  instiga o usuário a procurar a flag,
através de um cookie gerado por um login feito no site, onde  ao visualizar os cookie do site após o login feito, o usuário deve desvendar a flag através dele.
 <p>
Para que vc possa ler o desafio  <a href="https://play.picoctf.org/practice/challenge/469">clique aqui;</a>
</p>

Analise inicial

Primeiro era necessario se iniciar o launch instance no desafio, e então você era direcionado para uma página de login, onde era necessário que vc fizesse o login, com sua conta da picoCTF.
 Ao realizar o login, e acessar os cookie do site se obtinha um codigo no qual era necessário desvenda-lo para obter a flag.


Solução

Para resolver o desafio era necesssário iniciar o launch instance, e clicar no link disponibilazado e fazer o login.
Com o login feito, a pagina te gerava uma dica, instigando o usuário a verificar seus cookie.

[![acesso.png](https://i.postimg.cc/QC44273D/acesso.png)](https://postimg.cc/30GFpdNf)


Para verificação dos cookie, era necessário, ir nos tres  pontos do canto superior direito do site << mais ferramentas << console e digitar "document.cookie" e então se obitinha um codigo, no qual não era possivel decifrar, indicando ser uma mensagem cripitograda, com isso, utilizando o site CyberChef, era necessario copiar e colar o código no input do site, e com base nas características do codigo, era necessário aplicar filtro de URL decore,que ajudava na descodificação do codigo, e base64 assim  conseguia a flag.

<P>
Para entender mais, sobre base64 <a href= "https://tryhackme.com/p/base64">clique aqui;</a>
</p>      

flag
[![resultado.png](https://i.postimg.cc/rwWjJ3KM/resultado.png)](https://postimg.cc/75HSPB3W)

Conclusão


Com esse desafio podemos aprender melhor sobre como verificar um cookie, além de aprofundar um pouco mais em descodificação e como é uma base64.



