red.md

**introdução**

O desafio "RED" da PicoCTF, nos dá uma imagem que pode ser baixada, e instiga o usuário a saber se aquela imagem é realemnte pura, e oque se deve fazer para descobrir isso.

<a href="https://play.picoctf.org/practice/challenge/460">Pagina da CTF</a>

**Análise inicial**

Ao baixar a imagem no próprio navegador, só obtive uma imagem de um quadrado na cor vermelha.

[![red.png](https://i.postimg.cc/NG5n3gMx/red.png)](https://postimg.cc/mhWVMW7c)


Para desvenda-la utilizei o terminal com um comando de zsteg, para que eu pudesse ver se descobria algo. Com isso obtive uma string, na qual me pareceu estar codificada e então a descodifiquei obtendo a flag.



**Solução**


Primeiro baixei a imagem no meu terminal e logo após dei o comando de zsteg, usado para extrair dados ocultos em imagens png e bmp, e no resultado uma frase me chamou atenção parecendo estar codificada.

[![red.png](https://i.postimg.cc/HkCP8XtS/red.png)](https://postimg.cc/mt8S0cJM)
observe a linha (b1,rgba,lsb,xy      .. text:)





Após isso percebi que ela apresentava caractérisca de uma string em base64, já que possuia letras,maiúsculas e minúsculas, numéros e no final caracter especial como ==, portanto, apliquei novamente ao terminal com o comando echo a string entre aspas | e o comando base64 -d, para que a descodificasse e então obtive a flag do desafio.

[![red.png](https://i.postimg.cc/nLFR0xgB/red.png)](https://postimg.cc/VJhWkxYk)
**Conclusão**



Este desafio faz com que o usuário entenda um pouco mais de como desvendar algo através de uma imagem, que pode parecer simples e pura, porém pode não ser.
