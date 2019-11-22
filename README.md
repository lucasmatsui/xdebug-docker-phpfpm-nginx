## Ambiente Docker + Nginx + phpfpm + xdebug (VScode)
- Utilizando o gerador (<a href="https://phpdocker.io">PHPdocker.io</a>).
- Lembrando que estou no Linux Mint nesse tutorial.

## Configurando ambiente
- <b>Para você DEV hardcore apenas de um clone nesse repositorio e BOA SORTE!</b> :smile:.
- <b>Haa aqui não vai ter muita imagenzinha não muhahah</b>

- Para você que quer entender tudo certinho só seguir lendo (<b>Tem que ler tudo ta me ouvindo?</b>).

- Tenha o <b>docker</b> e o <b>docker-compose</b> instalados na sua máquina.
- E lembre-se de ter a extenção do <b>PHP Debug</b> no seu Vscode.
- Após entrar no gerador em <b>Global configuration</b> coloque o nome do seu projeto no campo <b>Project name</b> evite nomes com caracteres especiais, no máximo utilize: _ ou -
- Em <b>Base port</b> escolha a porta de acesso que não esteja sendo utilizada no momento, exemplo:
-- 3000
-- 8000
-- 8080
<b><i>OBS:</i></b> Nunca utilize a porta <b>9000</b> pois por padrão o phpfpm inicia usando essa porta.

- Escolha a versão do php, eu aqui estárei utilizando o php <b>7.2.x</b> e nas extenções apenas o <b>xDebug</b>, caso queira mais coisas é só selecionar.
- Depois disso não queremos mais nada, então desça até o final da página e clique em <b>Generate project archive</b>, ele gerara um aquivo <b>.zip</b> que você pode descompactar onde quiser :smile: .

- Abra a pasta do arquivo descompactado no seu VScode e vamos criar uma pasta para colocar nossos arquivos <b>.php</b>
- Vamos criar umas pasta chamada <b>public</b> e dentro dela um arquivo chamado <b>index.php</b>
```
// index.php

<?php
$hello = "alo";
echo $hello;
phpinfo();

// Não precisa fechar a tag php, vai ler as PSR's doido hehe.
```
- <b>OBS: </b> A pasta tem que estar com o nome <b>public</b> porque se não a configuração padrão que vem no nginx não vai achar a porcaria do seu arquivo <b>.php</b> :smile: .

- Se tudo ocorreu bem abra o seu terminal e execute:
```
$ docker-compose up -d
```
- Com isso ele vai gerar dois contêineres, um com o <b>nginx</b> e outro com o <b>php-fpm</b>.
- Você já pode acessar no seu navegador em <a>http://localhost:8000</a>(<b>na porta que voce setou viu queridão</b>). E a menssagem "alo" deve aparecer em cima e as configs do <b>phpinfo()</b> deverão aparecer em baixo.

- Só para garantir que está tudo certo, no navegador de <b>CTRL+F</b> e pesquise por <b>xdebug</b>, se aparecer isso quer dizer que funfo:
![xdebugworking](https://user-images.githubusercontent.com/31348487/69430353-2e914200-0d14-11ea-898e-936afa4dbd68.png)

- Beleza agora pode parar os contêineres para continuar as configurações, e <b>LEMBRE-SE</b> PARE os contêineres com:
```
$ docker-compose stop
```
Não me vai colocar <b>docker-compose down</b> que você vai ficar mudando o IP dos seus contêineres toda hora, lá na frente você vai entender a importância disso. E iniciar os contêiner é com:
```
$ docker-compose start
```

# Configurando o xdebug
- Agora um ponto <b>BAITA IMPORTANTE</b>, existe por ai muitos tutoriais ensinando a você configurar o XDebug alterando a porcaria do DockerFile do php-fpm.
- Amigo esquece isso, por isso dentro da pasta <b>/php-fpm</b> tem o bendito arquivo chamado chamado <b>php-ini-overrides.ini</b>, com esse nome de arquivo oque você acha que ele faz querido leitor. :smiley:, então não me vai relar a mão nesse DockerFile.

- Dentro DOOO! <b>php-ini-overrides.ini</b>, você vai colocar as seguintes linhas:
```
// php-ini-overrides.ini

xdebug.remote_host= <IP do Gateway do contêiner php-fpm>
xdebug.remote_enable=1
xdebug.remote_autostart=1
xdebug.remote_connect_back=1
xdebug.remote_port=9001
xdebug.idekey = VSCODE
``` 
- <b>OBS:</b> A porta tem que ser <b>9001</b> porque como eu disse anteriormente quem usa a porta <b>9000</b> é o php-fpm, pode até funcionar com as mesmas portas, porém você vai arriscar ?... eu não. 
- <b>MUITOO IMPORTE, O MAIS IMPORTANTE:</b> o valor de <b>xdebug.remote_host</b> tem que ser o IP do Gateway do contêiner php-fpm, para ver isso só procurar as configurações de rede do seu contêiner com:
```
$ docker inspect <ID do contêiner php-fpm>
```
- Agora vai la no Xdebug(no icone da joaninha do canto esquerdo) do seu VScode e clique na engrenagem e selecionem o PHP.
![xdebuginterface](https://user-images.githubusercontent.com/31348487/69432747-0b1cc600-0d19-11ea-8b7b-1f3c0f51f1a3.png)
- Com isso ele vai gerar umas pastinha no seu diretorio chamada <b>.vscode</b> e dentro dela um arquivo <b>lauch.json</b>
- Dentro desse arquivo  <b>lauch.json</b> vamos deixar ele da seguinte forma:
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9001,
            "log": true,
            "pathMappings": {
                "/application/public":"${workspaceRoot}/public"
            }
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 3000
        }
    ]
}
```
- E <b>FINALMENTE</b> podemos iniciar os contêineres novamente com:
```
$ docker-compose start
```
- Agora já podemos acessar nosso <b>localhost</b> e debugar nosso código apenas apertando <b>F5</b> no arquivo <b>index.php</b>. Para as variáveis no canto esquerdo do debug  aparecerem, depois de ter dado F5 no index.php você precisa dar também F5 na página, para ai sim aparecer tudo certinho para você debugar.

- E adicione um breakpoint no código se não se você iniciar o debug sem ele vai aparecer que não funcionou.


<b>Eu sei, eu sei, foi bem grande porém eu dei meu máximo para tentar te ajudar de alguma forma<b>

<b>Um grande abraço!</b> :smiley: .
