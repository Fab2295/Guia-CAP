# Sumário

- [Sumário](#sum-rio)
- [Criadores](#criadores)
- [Convenção de nomenclatura](#convenção-de-nomenclatura)
  * [Variáveis](#variáveis)
  * [Funções](#funções)
    + [Parâmetros](#parâmetros)
  * [Constante](#constante)
  * [Objetos](#objetos)
  * [Classes](#classes)
  * [Métodos privadas](#métodos-privadas)
  * [Array](#array)
  * [Comentários](#comentários)
- [Custom Handler](#custom-handler)
  * [Criando um handler](#criando-um-handler)
    + [Via CLI](#via-cli)
- [Teste Híbrido](#teste-híbrido)
  * [Conectar ao serviço no BTP](#conectar-ao-serviço-no-btp)
- [Deploy Cloud Foundry](#deploy-cloud-foundry)
  * [Pré-requisitos:](#pré-requisitos)
  * [Para versão @sap/cds-dk >= 8.9](#para-versão-@sap-/-cds-dk-89)
  * [Para a versão @sap/cds-dk < 8.9](#para-a-vers-o-sap-cds-dk-89)
  * [Via CAP Console](#via-cap-console)
- [Debug remoto Cloud Foundry](#debug-remoto-cloud-foundry)
  * [Pré-requisito](#pr-requisito)
  * [Para versão @sap/cds-dk >= 8.5](#para-versão-sap-cds-dk-85)
  * [Para versão @sap/cds-dk < 8.5](#para-versão-sap-cds-dk-85)
- [Zona de Perigo](#zona-de-perigo)
  * [SQL Injection](#sql-injection)
    + [Como não fazer](#como-não-fazer)
    + [Como prevenir](#como-prevenir)
  * [Condição de corrida](#condição-de-corrida)
    + [Como pode ser gerado via](#como-pode-ser-gerado-via)
    + [Como evitar](#como-evitar)
  
  <br>
  <br>
  
# Criadores:
- Dalcy Fabrício de Medeiros Neto
- Pedro Lázaro 
  
# Convenção de nomenclatura

Padrão que seguimos nos projetos CAP NodeJS (lembrando que na documentação do CAP não é falado sobre nenhum padrão de nomenclatura)

## Variáveis 

Utilize o padrão camelCase, iniciando a primeira palavra com letra minúscula e as palavras seguintes com a primeira letra maiúscula. Além disso, utilize nomes de variáveis autoexplicativos, evitando abreviações genéricas como x, y, z ou similares.

Exemplo bom: 

```js
let empresaGlobal;
var contadorErros = 0;
```

Exemplo ruim:

```js
var x = 0; // não da para saber qual seu uso com um nome x
let y;
```

## Funções

Mesmo princípio. 

Exemplo bom: 

```js
function recuperarDadosUsuario(idUsuario) {
    return dados;
} 
```

Exemplo ruim: 

```js
function retornar(id) {
    return dados;
}
```

### Parâmetros

Os parâmetros seguem a mesma convenção: começam com letra minúscula e cada nova palavra tem a primeira letra maiúscula (CamelCase). No Javascript, não há tipos para os parâmetros.

```js
function definirNome(nome) 
function verificarCliente(codigoDoCliente) 
``` 

## Constante 

Constantes, que são variáveis ​​que não mudam, devem ser escritas em maiúsculas. Se o nome contiver mais de uma palavra, use UPPER_SNAKE_CASE (letras maiúsculas com sublinhados entre as palavras). Exemplo:

```js
const EMPRESA_PADRAO = 1000;
const PORTA_EXECUCAO = 4004;
```

## Objetos

Para objetos seguir o padrão autoexplicativo e no singular. 

Exemplo:

```js
const usuario = {};
const cliente = {};
```

## Classes
Os nomes de classes devem seguir o padrão Pascal Case, o que significa que cada palavra no nome começa com uma letra maiúscula. Isso ajuda a diferenciar as classes de outros elementos JavaScript.

```js
class  DogCartoon { 
  constructor (dogName, ownerName) { 
    this.dogName = dogName; 
    this.ownerName = ownerName; 
  } 
} 

class  DogBreed { 
  constructor (breedName, breedSize) { 
    this.breedName = breedName; 
    this.breedSize = breedSize; 
  } 
}
```

## Métodos privadas
Use um sublinhado (_) como prefixo para indicar variáveis ​​ou funções privadas. Isso ajuda os desenvolvedores a entender que esses elementos não devem ser acessados ​​diretamente fora da classe ou do objeto ao qual pertencem.

```js
class  DogCartoon { 
  constructor ( dogName, ownerName ) { 
    this . dogName = dogName; 
    this . ownerName = ownerName; 
    this . name = _toonName (dogName, ownerName); 
  } 
  _toonName ( dogName, ownerName ) { 
    return  ` ${dogName}  ${ownerName} ` ; 
  } 
}
```

## Array

Para array de dados seguir o padrão autoexplicativo e no plural. 

Exemplo:

```js
const usuarios = [];
const clientes = []
```

## Comentários 

Seguimos o padrão de JSDOC para comentar funções/métodos/classes... (Não é um padrão que a SAP impõe, mas achamos útil).

Exemplo: 

```js
/**
 * Obtém as propriedades de uma mensagem internacionalizada a partir do CDS i18n.
 *
 * @private
 * @param {string} msg - Chave da mensagem a ser buscada no arquivo de internacionalização.
 * @returns {string} Retorna a msg de propriedades da mensagem localizada.
 * @throws {Error} Lança um erro caso o parâmetro `msg` não seja informado.
 */
_getMessageProperties(msg) {
  if (!msg) throw new Error('parametros não pode ser vazio');

  return cds.i18n.messages.at(msg);
}

```

Evite comentários para coisas obvias ou que não explicam nada. 

Exemplo do que não fazer:

```js
//Variavel guardar o valor 0 
let x = 0;
```

<br>

> Se a lógica for algo difícil de entender e/ou depende de regras complexas aí é bom colocar comentários com uma explicação mais detalhada para quando outro desenvolvedor for precisar entender seu código ele já tem uma explicação detalhada.


# Custom Handler

É uma forma de estender comportamentos padrões ou criar novos comportamentos programaticamente, via NodeJS ou java.

> Tente fazer o mais standard possivel, via anotações já existentes, só implemente um handler caso o standard não seja capaz de cubrir seu cenário

## Criando um handler

### Via CLI 

Uma maneira de criar uma casca do handler mais rápido é via um novo comando do cds add.

> Só foi adicionado a partir da versão @sap/cds-dk >= 8.5.0

Para criar via cli é necessário, além da versão, ter uma definifição de serviço no seu ```srv/``. Exemplo:

> Caso não possua pelo menos uma definição de serviço o comando gerará a mensagem: skipping, no model found

> É só um exemplo, pode ter N variações 

```cds
service MeuServico {
    entity Entidade as projection on Entidade;  
}
```

Com sua definição de serviço rode o comando:

```bash
cds add handler
```

será criado um arquivo com o nome do arquivo do seu serviço ou o que estiver na anotaçao ```@impl: 'nome-handler'```

> Será criado de forma generica, não espere que o comando vá implementar lógicas complexas. 

Arquivo gerado:

```js
import cds from '@sap/cds'

export class MyService extends cds.ApplicationService { 

init() {
  this.before (['CREATE', 'UPDATE'], Products, async (req) => {
    console.log('Before CREATE/UPDATE Products', req.data)
  })
  this.after ('READ', Products, async (products, req) => {
    console.log('After READ Products', products)
  })


  return super.init()
}}
```

# Teste Híbrido

Teste híbrido é uma forma de testar localmente via VSCode/BAS conectado a algum serviço do BTP.

> Para conseguir conectar seu projeto CAP a um serviço do BTP é preciso fazer o login via ```cf login```

## Conectar ao serviço no BTP

Para fazer o teste híbrido com um serviço no BTP, tipo: Hana

Rode o comando abaixo para criar a configuração de conexão.

> Esse comando vai gerar as configurações de acesso ao serviço em um arquivo chamado cdsrc-private.json

> Coloque ele no .gitignore para que não suba dado sensivel

```bash
cds bind -2 <servico-btp>
```

Com o sucesso, voccê pode rodar o comando:

```bash
cds watch --profile hybrid
```

Com isso irá se conectar ao serviço desejado e você poderá testar sem precisar deployar.


# Deploy Cloud Foundry

## Pré-requisitos:

Rodar o comando:

> Adicione suas credenciais, email e senha. Caso prefira login via SSO tem a variação do comando com --sso 

```bash
cf login 
```

Verifique se logou na subaccount certa:

```bash
cf target
```

## Para versão @sap/cds-dk >= 8.9

Depois de logado, rode o comando:

> Certifique-se de está logado na subaccount certa para evitar deployar no ambiente errado, para verificar se está logado certo, rode o comando ``` cf target ```

>  Aguarde o deploy ser concluido, em caso de erro verificar o log da aplicação


```bash
cds up
```

## Para a versão @sap/cds-dk < 8.9

Para a velha guarda, é necessário mais passos para o deploy. Primeiro rode o comando:

> Esse método também funciona para versão acima do 8.9, enquanto o cds up só funciona para versões acima do 8.9

> Esse comando gera o arquivo mtar com as informações do arquivo mta
> O mbt build também irá rodar o cds build --production 

```bash
mbt build -t gen --mtar mta.tar
```

ou simplesmente:

> O nome do arquivo será gerado a partir do <ID_do_mta>_<versão>

```bash
mbt build
```

Depois que criar o arquivo pode rodar o comando:

> O comando deploy do cf não existe por padrão (fora do BAS), para isso precisa instalar um plugin, consulte: https://cap.cloud.sap/docs/guides/deployment/to-cf#prerequisites 

```bash
cf deploy <caminho>/<arquivo.mtar|tar>
```

Aguarde o deploy ser concluido e em caso de erro consultar o log do terminal ou no BTP

## Via CAP Console 

> É uma ferramenta nova bugs podem acontecer...

Também pode olhar um novo programa desktop desenvolvido pela SAP chamado CAP Console:

https://cap.cloud.sap/docs/tools/console#setup-cap-console
https://tools.hana.ondemand.com/#cloud-capconsole

Um exemplo de como é a tela e dá para fazer várias coisinhas nele além de deploy, exemplo é acompanhar mensagens de log etc. E para monitoramento via CAP Console é preciso instalar a biblioteca: 

```bash
npm i @cap-js/console
```

# Debug remoto Cloud Foundry

Dependendo do cenário temos que debugar, nem sempre conseguimos debugar de forma hibrida, ai para isso existe o debug via tunelamento de SSH.

## Pré-requisito 

Primeiro verifique se o ssh está habilitado para o space e aplicação.

Primeiro faça o login: 

```bash
cf login
```

Verifique se o space está com o suporte a SSH ligado

```bash
cf space-ssh-allowed <nome-space>
```

Caso não esteja, rode o comando:

```bash
cf allow-space-ssh <nome-espace>
```

Verifique se a aplicação está com o suporte a SSH habilitado, para isso rode: 

```bash
cf ssh-enabled <sua-aplicação>
```

caso não esteja ligado, rode:

```bash
cf enable-ssh <sua-aplicação>
```

Com sucesso, precisará reiniciar a aplicação, para isso rode:

```bash
cf restart <sua-aplicação>
```

Adicione a configuração do debug remoto (esse passo não precisa ser o último, não depende dos passos anteriores para ser feito).
No caminho .vscode/launch.json gere essa configuração:

> Adicione toda a configuração, mas o que seria necessario para nosso debug é a primeira configuraçõ da propriedade ```configurations```

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "address": "127.0.0.1",
      "localRoot": "${workspaceFolder}",
      "name": "Attach to Remote",
      "port": 9229,
      "remoteRoot": "Absolute path to the remote directory containing the program",
      "request": "attach",
      "skipFiles": [
        "<node_internals>/**"
      ],
      "type": "node"
    },
    {
      "name": "cds serve",
      "request": "launch",
      "type": "node",
      "cwd": "${workspaceFolder}",
      "runtimeExecutable": "cds",
      "args": [
        "serve",
        "--with-mocks",
        "--in-memory?"
      ],
      "skipFiles": [
        "<node_internals>/**"
      ]
    }
  ]
}

```

## Para versão @sap/cds-dk >= 8.5

Rode o comando:

> Simples assim, porém ele não funciona bem no BAS, pois o comando tenta abrir o chrome, e no BAS ele não consegue, então rode no VSCode

```bash
cds debug <nome-aplicação-deployada>
```

## Para versão @sap/cds-dk < 8.5

Para versões menores é um pouco mais trabalhoso.

> Verifique os pré-requisitos para habilitar o ssh

```bash
cf ssh <sua-aplicação>
```

caso ocorra sucesso, você estará conectado ao terminal remoto. Agora rode:

```bash
ps aux | grep node
```

Encontre o processo do node que roda o cds-serve, encontrando ele pegue o id do processo. No meu caso é o ID 263 (esse ID não é fixo, pode e vai mudar) e rode o comando:

```bash
kill -usr1 <id>
```

Abra outro terminal e rode:

> A porta 9229 é a porta padrão do debugger do NodeJS

> 127.0.0.1 é o endereço de loopback, também conhecido como localhost

> Variação do comando mais famosa é por o -N para não abrir o terninal remoto 

```bash
cf ssh <sua-aplicação> -L 9229:127.0.0.1:9229
```

Agora escolha no menu do debug do VSCode/BAS e escolha a config do debug remoto (passo no pré-requisito)

![](assets/17665521691792.jpg)



Depois é so abrir o loaded scripts e vê os arquivos remotos e colocar break onde achar necessário.

![](assets/17665524886600.jpg)



<br>
<br>

# Zona de Perigo

## SQL Injection

A injeção de Structured Query Language (SQL*) é uma técnica de injeção de código usada para modificar ou recuperar dados de bancos de dados SQL. Ao inserir instruções SQL especializadas em um campo de entrada, um invasor pode executar comandos que permitem a recuperação de dados do banco de dados, a destruição de dados sensíveis ou outros comportamentos manipuladores.

### Como não fazer

Não usar string concatenada no Where, exemplo:

Serviço 

```cds
@path: `service`
service MyService {
    action Acao(campo: string(255)) return Map;
}
```

Handler

```js
this.on('Acao', async (req) => {
    return await SELECT.from(this.entities.Tabela).where('campo = ' + req.data.campo)
})
``` 

Vê algum problema ? 

E funciona, porém se eu passar algum código malicioso que nem esse no campo:

```json
{
 "campo": "1; DELETE FROM Tabela;"
}
```

Ou até 

```json
{
 "campo": "1 or 1 = 1"
}
```

### Como prevenir

Usar o formato de objeto é uma maneira, mesmo que eu passe um script malicioso ele não executaria, como está parametrizada ele entende que o valor passado independente se é um comando ou não será um valor a ser consultado direto no campo.

> Essa é uma forma, existe outras, qualquer dúvida consultar a documentação oficial: https://cap.cloud.sap/docs/node.js/cds-ql#avoiding-sql-injection


```js
this.on('Acao', async (req) => {
    return await SELECT.from(this.entities.Tabela).where({
        campo: req.data.campoÏ
    })
})
``` 


## Condição de corrida 

Uma condição de corrida ocorre quando duas ou mais operações acontecem ao mesmo tempo, e o resultado depende de qual delas termina primeiro.

### Como pode ser gerado via

usar await em eventos sincronos

> Detalhe, o CAP já conecta você ao db, não precisa conectar novamente


```js
cds.on('served', async ()=>{
  const db = await cds.connect.to('db') // DANGER: will cause race condition !!!
  db.on('before',(req)=> console.log(req.event, req.path))
})
```

Via mais de um after em uma mesma requisição:

> Todos os manipuladores .after são executados em paralelo, o que pode levar a condições de corrida caso vários manipuladores se apliquem à mesma requisição. Portanto, use com cautela!

> Pode usar o after each que é sincrono https://cap.cloud.sap/docs/node.js/core-services#srv-after-request

```js
this.after('READ', this.entities.Tabela, (data, req) => {
    console.log("faz algo");
})

this.after('READ', this.entities.Tabela, (data, req) => {
    console.log("faz algo");
})
```

### Como evitar

Uso do cds.services 

```js
cds.on('served', ()=>{
  const { db } = cds.services
  db.on('before',(req)=> console.log(req.event, req.path))
})
```

















