interencdec.md

**Introdução**

No desafio "interencdec" da PicoCTF, recebemos um arquivo para download. O objetivo é utilizar o terminal para decodificá-lo e revelar a flag escondida.


<a href="https://play.picoctf.org/practice/challenge/418">Pagina da CTF</a>

**Análise inicial**


Após baixar o arquivo para o terminal, utilizei o comando **cat** para visualizar seu conteúdo. A string resultante parecia ser uma codificação em Base64, e a repetições de caracteres específicos reforçaram essa hipótese. Decidi decodificá-la duas vezes usando Base64. A string final obtida me pareceu ser um texto criptografado que seguia o formato de flag da PicoCTF, e após uma análise mais detalhada, presumi que se tratava de uma **Cifra de César**.


**Solução**

Primeiro baixei o arquivo, utilizando o comando wget e copiando o url do arquivo que estava no site, com o arquivo baixado em meu terminal, dei o comando do **cat**, responsável por ajudar a ler oq está dentro do arquivo, com isso obtive minha primeira string.

<img src="1.png" alt= .  >




Após isso notei que pelas características se tratava de uma string de base64, e então dei ao terminal o comando com **echo a tring codificada | base64 -d**  e assim consegui uma nova string que também me pareceu de base64 com isso repeti o mesmo processo e obtive uma ultima string.





<img src="2.png" alt= .>

Este processo me deu a string final que, mesmo parecendo uma flag, estava codificada. Para desvendar a codificação, utilizei o site dCode. Ao colar a string, a própria ferramenta indicou que se tratava de uma Cifra de César, e a decodificou automaticamente, revelando a flag final.

<img src="3.png" alt= flag>

<a href="https://www.dcode.fr/caesar-cipher">Site dCode</a>

**Conclusão**


Este desafio me instigou a pensar em decriptografia por etapas, aprofundando meus conhecimentos em Base64 e Cifra de César. A necessidade de ir além do que era imediatamente aparente me fez analisar o problema de forma mais aprofundada, demonstrando a importância de identificar diferentes camadas de criptografia.