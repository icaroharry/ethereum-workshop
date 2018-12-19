# Ethereum workshop: Adoção de animais

#### NOTA: Esse tutorial é baseado na versão do tutorial da Swapy Network que por sua vez é baseado no [Pet Shop tutorial](http://truffleframework.com/tutorials/pet-shop) do Truffle. Todo o conteúdo é puramente didático e não deve ser usado em main net!

## Conteúdo

- [Introdução](#introdu%C3%A7%C3%A3o)
- [Ambiente](#ambiente)
- [Desenvolvimento](#desenvolvimento)
  - [Estrutura de pastas](#estrutura-de-pastas)
  - [Escrevendo os Smart Contracts](#escrevendo-os-smart-contracts)
    - [Criando o contrato e definindo a versão do compilador](#criando-o-contrato-e-definindo-a-vers%C3%A3o-do-compilador)
    - [Criando variáveis](#criando-vari%C3%A1veis)
    - [Criando a primeira função: Adicionar um animal para adoção](#criando-a-primeira-fun%C3%A7%C3%A3o-adicionar-um-animal-para-ado%C3%A7%C3%A3o)
    - [Criando a segunda função: Retornar o número total de animais](#criando-a-segunda-fun%C3%A7%C3%A3o-retornar-o-n%C3%BAmero-total-de-animais)
    - [Criando nosso primeiro modificador de funções](#criando-nosso-primeiro-modificador-de-fun%C3%A7%C3%B5es)
    - [Criando nossa terceira função: Retornar os dados de um animal](#criando-nossa-terceira-fun%C3%A7%C3%A3o-retornar-os-dados-de-um-animal)
    - [Criando nossa quarta função: Adotar um animal](#criando-nossa-quarta-fun%C3%A7%C3%A3o-adotar-um-animal)
- [Compilando e publicando os contratos](#compilando-e-publicando-os-contratos)
  - [Compilando o contrato](#compilando-o-contrato)
  - [Publicando o contrato](#publicando-o-contrato)
- [Testando o contrato](#testando-o-contrato)
  - [Testando a função: addPet](#testando-a-fun%C3%A7%C3%A3o-addpet)
  - [Testando a função: getNumberOfPets](#testando-a-fun%C3%A7%C3%A3o-getnumberofpets)
  - [Testando a função: getPet](#testando-a-fun%C3%A7%C3%A3o-getpet)
  - [Testando a função: adopt](#testando-a-fun%C3%A7%C3%A3o-adopt)
  - [Executando os testes](#executando-os-testes)
- [Criando uma interface para interagir com o smart contract](#criando-uma-interface-para-interagir-com-o-smart-contract)
  - [Instanciando o web3](#instanciando-o-web3)
  - [Instanciando o contrato](#instanciando-o-contrato)
  - [Consultando os pets adotados e atualizando a UI](#consultando-os-pets-adotados-e-atualizando-a-ui)
  - [Adicionando um novo pet](#adicionando-um-novo-pet)
  - [Adotando um pet](#adotando-um-pet)
- [Interagindo com o dapp no browser](#interagindo-com-o-dapp-no-browser)
  - [Instalando e configurando o MetaMask](#instalando-e-configurando-o-metamask)
- [Iniciando o servidor http local](#iniciando-o-servidor-http-local)
- [Usando o dapp](#usando-o-dapp)
- [Parte final](#parte-final)
  - [Desafio 1](#desafio-1)
  - [Desafio 2](#desafio-2)
  - [Desafio 3](#desafio-3)

  
## Introdução

Nossa aplicação consiste em um sistema para o controle de adoções de animais em um Pet Shop e, para isso, esse tutorial aborda o processo de:

* Configurar o ambiente de desenvolvimento
* Escrever os smart contracts
* Compilar e publicar os contratos
* Testar os contratos 
* Criar uma interface web para o usuário
* Interagir com o aplicativo descentralizado através do browser

## Ambiente

Antes de começarmos a desenvolver nossa aplicação existem alguns requisistos técnicos. Instale o que segue:

* [Node.js v6+ LTS e npm](https://nodejs.org/en/) e npm (é instalado junto com o Node)
* [Git](https://git-scm.com/)

Após isso, nós precisamos somente instalar o Truffle, o framework que auxiliará no desenvolvimento do nosso DApp:

```
npm install -g truffle
```
Para verificar se o Truffle está instalado corretamente, digite ``` truffle version ``` no seu terminal. Caso veja algum erro, certifique-se que seus módulos npm estão visíveis nas variáveis de ambiente 

Utilizaremos também a Ganache, uma blockchain privada que permite a publicação dos nossos contratos em ambiente de desenvolvimento e dará suporte para o uso e teste da nossa aplicação. Faça o download da mesma em http://truffleframework.com/ganache.

## Desenvolvimento

Primeiramente, utilize o Git para clonar este repositório em sua máquina, na pasta que desejar
```
git clone git@github.com:icaroharry/ethereum-workshop.git
```
Navegue até a pasta raiz do projeto e instale as dependências locais com
```
npm install
```

### Estrutura de pastas

A estrutura inicial de pastas fornecida pelo Truffle possui:

* ```contracts/```: Contém o código fonte dos nossos contratos na linguagem [Solidity](https://solidity.readthedocs.io/en/develop/)
* ```migrations/```: Responsável por migrar nossos contratos para o ambiente desejado. O Truffle possui um sistema de controle de alterações.
* ```test/```: Contém os testes em Javascript e Solidity
* ```truffle.js```: Arquivo de configuração onde é descrito, por exemplo, o host dos contratos. Utilizaremos a rede local fornecida pela Ganache 

Esse repositório possui outras pastas que não importam no momento e serão descritas no decorrer do tutorial.

### Escrevendo os Smart Contracts

Nós começaremos nosso aplicativo descentralizado escrevendo os contratos, que atuam como o "back-end" e criam a interface para o armazenamento na blockchain.

#### Criando o contrato e definindo a versão do compilador
1. Crie um novo arquivo de nome ```Adoption.sol``` na pasta ```contracts/``` .

2. Adicione esse conteúdo ao arquivo:

```
pragma solidity ^0.4.17;

contract Adoption {

}
```
Observações:

* A versão mínima requerida do Solidity é descrita no ínicio do contrato: ```pragma solidity ^0.4.17;```. A palavra-chave ```pragma``` significa "informação adicional que importa somente ao compilador", enquanto o símbolo ```^``` significa "A versão indicada ou superior".
* Assim como em outras linguagens, a sintaxe exige ```;``` ao final de cada comando.

#### Criando variáveis

O Solidity é uma linguagem estaticamente tipada, isso significa que dados do tipo string, integer e array devem ser definidos. Nesse tutorial utilizaremos um tipo de variável único da linguagem, chamado ```address```. Esse tipo representa um endereço Ethereum, uma string de 20 bytes com funcionalidades específicas. Toda carteira (wallet) e smart contract possui um endereço e pode enviar Ethers através/para ele ou mesmo realizar chamadas para funções. 

1. Para representar um animal a ser adotado, definiremos uma estrutura ```Pet```. Insira a Struct ```Pet``` que segue na próxima linha após ```contract Adoption {```.
```
    struct Pet {
        bytes name;
        address adopter;
    }

```
2. Após isso, precisamos definir nossa lista de animais. Cole o código abaixo após a definição de ```Pet```:
```
    Pet[] public pets;
```
Observações:

* Definimos uma única variável ```pets``` . Arrays possuem um tipo e podem ter tamanho fixo ou variável. No caso, nossa lista é do tipo ```Pet``` e possui tamanho variável.

* Nossa variável é do tipo ```public```. Variáveis públicas possuem automaticamente um método getter associado a elas. Entretanto, no caso de arrays, o acesso é restrito a um item por vez, pela necessidade de passar uma chave na chamada do getter. 

* #### Os modificadores ```public``` e ```private``` não referem-se à confidencialidade do dado na blockchain. Todos os dados são visíveis.

#### Criando a primeira função: Adicionar um animal para adoção

Vamos permitir a inserção de animais na nossa lista ```pets```.

1. Adicione a seguinte função abaixo da variável que definimos anteriormente
```
    // adding a pet
    function addPet(bytes name) public returns(uint) {
        Pet memory newPet = Pet(name, address(0));
        pets.push(newPet);
        return pets.length - 1;
    }
```
Observações:

* Precisamos definir o tipo dos parâmetros e do retorno, quando existir, das funções no Solidity. Nesse caso, recebemos uma cadeia de ```bytes``` (string) que representa o nome do novo animal e retornamos um inteiro que indica o índice do novo registro na lista.

* Criamos um novo registro do tipo ```Pet``` a partir do nome recebido e inicializamos o endereço do possível adotante com um valor vazio ```address(0)```, pois o mesmo ainda não está definido quando o animal é inserido para adoção.

* A palavra-chave ```memory``` aparece por uma necessidade da linguagem de se explicitar que essa variável está sendo criada na memória, até o momento.

* Inserimos então o animal à lista e retornamos seu índice.

#### Criando a segunda função: Retornar o número total de animais 

Como já foi dito ao definirmos nossa lista de animais, só conseguimos acessar os itens individualmente. Dessa forma, incluiremos uma função para saber o tamanho da nossa lista e facilitar futuros controles através da aplicação cliente.

1. Adicione a seguinte função abaixo de ```addPet```, definida no passo anterior
```
    // Retrieving number of pets
    function getNumberOfPets() public view returns (uint) {
        return pets.length;
    }
```
Observações:

* A presença do modificador ```view``` significa que essa função não altera o estado de nenhuma variável do nosso contrato ou realiza chamadas internas a outros contratos com esse propósito.

#### Criando nosso primeiro modificador de funções

Ao buscar um animal e/ou agir sobre ele, precisamos checar se o índice recebido por parâmetro é compreendido no array, ou seja, se o animal existe. Aproveitaremos então um recurso do Solidity que é o ```modifier```.  

1. Adicione a definição do modificador ```validPet``` acima da primeira função presente no contrato.
```
    modifier validPet(uint petId) {
        require(petId >= 0 && petId < pets.length);
        _;
    }
```
Observações:

* O ```require(<check>)``` é utilizado para lançar uma exceção e reverter a execução do código se ```<check>``` for falso.
* O símbolo ```_``` serve para injetar a execução da função interceptada pelo modificador após a validação, isso será melhor entendido no passo seguinte.

#### Criando nossa terceira função: Retornar os dados de um animal

Vamos permitir que nossa aplicação tenha acesso aos dados de um animal inserido.

1. Adicione a função descrita abaixo após a função ```addPet```.
```
    // Retrieving a pet
    function getPet(uint petId) 
        validPet(petId)
        public 
        view 
        returns(bytes, address) 
    {
        return (pets[petId].name, pets[petId].adopter);
    }
```
Observações: 

* Inserimos o modificador ```validPet``` na assinatura do método, passando o índice do animal requerido. Sendo assim, ele agirá como um interceptador da função e continuará sua execução se o animal existir na lista. 
* Tipos não primários da linguagem, como é o caso da nossa Struct ```Pet```, não conseguem ser lidos pelo client até o momento, por uma deficiência da tecnologia. Para contornar isso, precisamos retornar o animal em forma de tupla, representada por ```(pets[petId].name, pets[petId].adopter)```. Perceba que os tipos dos elementos que compõem a tupla também precisam ser descritos no retorno da função ```returns(bytes, address)```

#### Criando nossa quarta função: Adotar um animal

Após adicionar um animal e conseguir visualiza-lo externamente, precisamos criar a funcionalidade de adotar.

1. Adicione o que segue após a declaração da função ```getPet```.
```
    // Adopting a pet
    function adopt(uint petId) 
        validPet(petId)
        public 
        returns (uint) 
    {
        Pet storage pet = pets[petId];
        pet.adopter = msg.sender;
        pets[petId] = pet;
        return petId;
    }
```
Observações: 

* A palavra-chave ```storage``` indica que essa variável está sendo trabalhada no storage do contrato, ao contrário de ```memory```.
* O Solidity possui uma variável global ```msg``` que é preenchida a cada transação. Utilizamos a propriedade ```msg.sender``` para pegarmos o endereço da carteira que realizou essa transação e preenchermos como o adotante do animal em questão.

## Compilando e publicando os contratos

Agora que já desenvolvemos o contrato, os próximos passos são compilar e publicar na nossa rede local.

A partir desse momento utilizaremos o Truffle, que possui um console de desenvolvimento embutido com funções que nos auxiliam nos testes, compilação e publicação.

### Compilando o contrato

O Solidity é uma linguagem compilada, o que significa que temos que transformar nosso contrato em bytecodes para que EVM (Máquina virtual Ethereum) consiga executa-lo.

1. Navegue até a pasta raiz do projeto e digite o comando
```
truffle compile
```
O resultado deve ser parecido com o que segue
```
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/Adoption.sol...
Writing artifacts to ./build/contracts
```

### Publicando o contrato

Após compilarmos nosso contrato, resta agora publica-lo na blockchain!

1. Navegue até a pasta ```migrations/```, você deve ver um arquivo JavaScript ```1_initial_migration.js```
2. Crie um arquivo de nome ```2_deploy_contracts.js``` nesse mesmo diretório.
3. Adicione o conteúdo que segue no arquivo criado anteriormente
```
var Adoption = artifacts.require("Adoption");

module.exports = function(deployer) {
  deployer.deploy(Adoption);
};
```
4. Antes de publicarmos nosso contrato, precisamos que a nossa blockchain local esteja rodando. 
Como dito na configuração do ambiente, utilizaremos a Ganache como blockchain de desenvolvimento.
Certifique-se que ela está instalada em sua máquina e dê um clique duplo em seu ícone. Após isso, ela iniciará e criará nossa rede local na porta 7545.

5. Voltando ao terminal, digite o comando

```
truffle migrate --reset
```
O resultado deve ser parecido com o que segue
```
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xcc1a5aea7c0a8257ba3ae366b83af2d257d73a5772e84393b0576065bf24aedf
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying Adoption...
  ... 0x43b6a6888c90c38568d4f9ea494b9e2a22f55e506a8197938fb1bb6e5eaa5d34
  Adoption: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
Saving successful migration to network...
  ... 0xf36163615f41ef7ed8f4a8f192149a0bf633fe1a2398ce001bf44c43dc7bdda0
Saving artifacts...
```
Ao lado do nome de cada contrato temos o endereço dele na rede.

6. Na Ganache, note que o estado da blockchain alterou. Agora a rede mostra que o bloco atual é o 4, e não mais o 0. Além disso, verificamos que o saldo anterior de 100 Ethers foi reduzido, devido ao custo para realizar transações na rede (GAS).

Agora que temos nosso contrato disponível na blockchain, é o momento de interagirmos com ele.


## Testando o contrato

O Truffle é bastante flexível no que se refere aos testes de smart contracts. O desenvolvedor é livre para criar os testes em JavaScript ou na própria linguagem Solidity. Nesse tutorial escreveremos os testes em Solidity.

1. Navegue até a pasta ```test/``` e crie um arquivo de nome ```TestAdoption.sol```

2. Adicione o código abaixo no arquivo criado
```
pragma solidity ^0.4.17;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "./ThrowProxy.sol";
import "../contracts/Adoption.sol";


contract TestAdoption {
    Adoption adoption = Adoption(DeployedAddresses.Adoption());
    ThrowProxy throwProxy = new ThrowProxy(address(adoption)); 
    Adoption throwableAdoption = Adoption(address(throwProxy));
}
```
Assim, iniciamos o contrato com quatro importantes importações:

* ```Assert.sol```: Provê funções para testes de igualdade, desigualdade ou retornos errôneos. [Aqui](https://github.com/trufflesuite/truffle-core/blob/master/lib/testing/Assert.sol) você pode consultar a lista completa de funções para teste incluidas no Truffle.
* ```DeployedAddresses.sol```: Quando rodamos os testes, o  Truffle publica temporariamente os contratos na blockchain. Esse contrato é responsável por armazenar os endereços desses contratos no ambiente de teste.
* ```Adoption.sol```: O Contrato que será testado.

No escopo do contrato ```TestAdoption``` utilizaremos a instância ```adoption``` para testarmos o sucesso das funções.

Observação:

* O caminho ```trufle/<contract>.sol``` refere-se à dependência instalada no momento de configuração do ambiente, portanto, não crie uma pasta ```truffle/``` dentro do diretório de testes ```test/```

### Testando a função: addPet

1. Adicione o código que segue após as declarações iniciais do contrato ```TestAdoption```
```
// Testing the addPet() function
  function testUserCanAddPet() public {
    uint returnedId = adoption.addPet("Dog Test");
    uint expected = 0;
    Assert.equal(returnedId, expected, "pet ID 0 should be recorded.");
  }
```
Observações:

* Nesse teste criamos um animal de nome "Dog Test" 
* Então, esperamos que esse novo animal seja o primeiro da lista.

### Testando a função: getNumberOfPets

1. Adicione o código que segue ao final do contrato ```TestAdoption```
```
 // Testing the getNumberOfPets() function
    function testUserCanGetTheNumberOfPets () public {
        uint expectedPetLength = 1;
        uint petLength = adoption.getNumberOfPets();
        Assert.equal(petLength, expectedPetLength, "Number of pets should be 1.");
    }
```
Observações:

* Nesse teste buscamos o número de animais existentes para adoção.
* Então, esperamos que só exista um animal na lista.

### Testando a função: getPet

1. Adicione o código que segue ao final do contrato ```TestAdoption``` para o teste
de busca de um animal válido.
```
  // Testing the getPet() function
  function testUserCanGetPet() public {
    // Expected adopter is null
    address expectedAdopter = address(0);
    var (,adopter) = adoption.getPet(0);
    Assert.equal(adopter, expectedAdopter, "Adopter of pet ID 0 should be empty.");
  }
```

Observações:

* Nesse teste buscamos o primeiro animal da lista.
* Então, esperamos que esse animal ainda não tenha sido adotado.
* Podemos fazer referências a tuplas omitindo variáveis que não desejamos, como em
```
var (,adopter) = adoption.getPet(0);
```

### Testando a função: adopt
1. Adicione o código que segue ao final do contrato ```TestAdoption``` para o teste
de adoção de um animal válido.
```
  // Testing the adopt() function
  function testUserCanAdoptPet() public {
    uint returnedId = adoption.adopt(0);
    uint expectedId = 0;
    address expectedAdopter = this;
    Assert.equal(returnedId, expectedId, "Adoption of pet ID 0 should be recorded.");
    var (,adopter) = adoption.getPet(0);
    Assert.equal(adopter, expectedAdopter, "Adopter of pet ID 0 should be the TestAdoption contract.");
  }
```

Observações:

* Nesse teste adotamos o primeiro animal da lista.
* Então, esperamos que o animal adotado ao final do processo seja o mesmo.
* Quando um contrato executa uma chamada de alto nível a outro contrato, a variável global ```msg``` também é alterada, ou seja, o ```msg.sender``` desse teste é o endereço do próprio contrato ```TestAdoption```. Isso torna o ```TestAdoption``` o adotante do animal.

### Executando os testes

Semelhante aos procedimentos ```compile``` e ```migrate```, utilizaremos o comando
```test``` fornecido pelo Truffle para rodarmos os testes.

1. Navegue até a pasta raiz do projeto e execute 
```
truffle test
```

2. Se todos os testes passarem, você verá um resultado similar ao que segue
```
Using network 'development'.

   Compiling ./contracts/Adoption.sol...
   Compiling ./test/TestAdoption.sol...
   Compiling truffle/Assert.sol...
   Compiling truffle/DeployedAddresses.sol...

     TestAdoption
       ✓ testUserCanAddPet (50ms)
       ✓ testUserCanGetTheNumberOfPets (50ms)
       ✓ testUserCanGetPet (50ms)
       ✓ testUserCanAdoptPet (50ms)
       ✓ testUserCannotGetAnInvalidPet (50ms)
       ✓ testUserCannotAdoptAnInvalidPet (50ms)


     6 passing (300ms)
```


## Criando uma interface para interagir com o smart contract

Agora que nós criamos o smart contract, fizemos o deploy para nossa blockchain local e interagimos com ele via console, é hora de criar uma UI para que os donos de petshop possam utilizá-lo.

Junto com os smart contracts, nesse repositório, há uma pasta `src/` com o código para o front-end.

O front-end não usa nenhum sistema de build (webpack. grunt, etc) e nenhum framework ou biblioteca javascript (Angular, React, Vue, etc) para ser o mais simples possível para começar. A estrutura do app já está disponível nessa pasta; na próxima etapa do tutorial nós vamos preencher as funções relevantes para interagir com o Ethereum. Dessa forma, você pode pegar esse conhecimento e aplicar da forma como preferir no seu *workflow*.

### Instanciando o web3

1. Abra `src/js/app.js` no seu editor de texto.
2. Leia o arquivo. Note que há um objeto global `App` para gerenciar a aplicação, carregar os dados do petshot na função `init()` e chamar a função `initWeb3()`. A [biblioteca JavaScript web3](https://github.com/ethereum/web3.js/) interage com a blockchain do Ethereum. Ela pode acessar carteiras, assinar e enviar transações, interagir com os smart contracts, etc.
3. Remova o comentário de dentro de `initWeb3()` e cole o seguinte código:

```javascript
// Is there an injected web3 instance?
if (typeof web3 !== 'undefined') {
  App.web3Provider = web3.currentProvider;
} else {
  // If no injected web3 instance is detected, fall back to Ganache
  App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
}
App.web3 = new Web3(App.web3Provider);

return App.initContract();
```

Observações:
- Primeiro, nós checamos se há alguma instância do web3 ativa. Browsers Ethereum como o [Mist](https://github.com/ethereum/mist) ou a extensão [MetaMask](https://metamask.io/) injetam sua própria instância da web3. Se há alguma instância na `window`, nós pegamos o seu `provider` e usamos para criar a nossa instância da web3.
- Se nenhuma instância da web3 está presente, nós criamos a nossa usando o nosso provider local.

### Instanciando o contrato
Agora que nós podemos interagir com o Ethereum via web3, nós precisamos instanciar os nossos smart contracts para que o web3 saiba encontrá-lo e utilizá-lo. Nesse tutorial, estamos utilizando uma biblioteca do Truffle chamada [`truffle-contract`](https://github.com/trufflesuite/truffle-contract). Ela mantém as informações do contrato sincronizadas com as *migrations*, assim você não precisa alterar os endereços dos contratos manualmente.

Nessa etapa, é interessante destacar como o web3 consegue entender o smart contract e  se comunicar com ele: ao compilar o código solidity um arquivo `.json` é criado. Esse arquivo é chamado de [ABI (Application Binary Interface)](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI). Ele contém metadados do contrato, como as suas variáveis, sua funções e os respectivos tipos e retornos. Dessa forma, se o seu contrato possui a seguinte função: 

```solidity
function sum(uint x, uint y) public returns (uint) {
  return x + y;
}
```

Através da ABI, o web3 vai entender que o contrato possui uma função `sum` que deve receber dois inteiros por parâmetro e retornar outro inteiro:

```javascript
myContractInstance.sum.call(x, y, (error, result) => {
  assert(!error);
  assert(result).equals(x + y);
});
```

Prosseguindo com o tutorial:
1. Ainda no arquivo `src/js/app.js`, remova o bloco de comentários de dentro da função `initContract()` e inclua o seguinte trecho de código:

```javascript
$.getJSON('Adoption.json', function(data) {
  // Get the necessary contract artifact file and instantiate it with truffle-contract
  var AdoptionArtifact = data;
  App.contracts.Adoption = TruffleContract(AdoptionArtifact);

  // Set the provider for our contract
  App.contracts.Adoption.setProvider(App.web3Provider);

  // Use our contract to retrieve and mark the adopted pets
  return App.loadPets();
});

return App.bindEvents();
```

Observações:

- Primeiro pegamos a ABI do contrato.
- Após recebermos o JSON no callback, nós o passamos para `TruffleContract()`. Isso irá criar a instância do contrato com a qual podemos interagir.
- Com o nosso contrato instanciado, nós setamos o seu *web3 provider* usando o `App.web3Provider` que havíamos iniciado quando configuramos o web3.
- Depois nós chamamos a função `loadPets()` para carregar os pets que ja foram cadastrados anteriormente. Nós encapsulamos isso em uma função separada já que vamos precisar de atualizar a UI toda vez que fizermos alguma modificação na blockchain.

### Consultando os pets adotados e atualizando a UI

1. Ainda em `src/js/app.js`, remova o bloco de comentário em `loadPets` e altere para:

```javascript
   var adoptionInstance;
    App.contracts.Adoption.deployed().then(function(instance) {
      adoptionInstance = instance;
      return adoptionInstance.getNumberOfPets.call();
    }).then(function(res) {
      var numberOfPets = res.toNumber(); // number of pets is a BigNumber

      if (numberOfPets > 0) {
        $('#add-one').hide();
        $('.btn-addPet').show();

        var promises = [];
      
        for(i = 0; i < numberOfPets; i++) {
          promises.push(adoptionInstance.getPet.call(i))
        }
  
        Promise.all(promises).then(function(result) {
          var petsRow = $('#petsRow');
          var petTemplate = $('#petTemplate');
          petsRow.empty();
        
          for(i = 0; i < numberOfPets; i++) {
            petTemplate.find('.panel-title').text(App.web3.toAscii(result[i][0]));
            petTemplate.find('img').attr('src', App.images[Math.floor(Math.random() * App.images.length)]);
            petTemplate.find('.btn-adopt').attr('data-id', i);
            result[i][1] != '0x0000000000000000000000000000000000000000' ?
              petTemplate.find('.btn-adopt').text('Adopted').attr('disabled', true) :
              petTemplate.find('.btn-adopt').text('Adopt').attr('disabled', false);
  
            petsRow.append(petTemplate.html());
          }
        });
      } else {
        $('.btn-addPet').hide();
        $('#add-one').show();
      }
    }).catch(function(err) {
      console.log(err.message);
    });
```

Observações:
- Nós acessamos o contrato `Adoption` que foi feito deploy em nossa blockchain local e chamamos `getNumberOfPets` nessa instância.
- Primeiro, nós declaramos a variável `adoptionInstance` fora da chamada do smart contract, para que possamos acessar a instância depois de recuperá-la da blockchain.
- Usar a função `call()` nos permite ler os dados da blockchain sem alterar o seu estado, ou seja, sem enviar uma "transação completa". Dessa forma, não gastamos gas.
- Após chamar `getNumberOfPets()`, nós fazemos um loop e chamamos a função `getPet()` para buscar os dados de cada pet.
- Por padrão, os valores são instanciados com um endereço vazio no Solidity, portanto quando o valor retornado é um endereço diferente de `0x0000000000000000000000000000000000000000`, sabemos que há um endereço e portanto o pet foi adotado.

### Adicionando um novo pet
Inicialmente, o nosso contrato não possui nenhum pet cadastrado, portanto precisamos adicionar. Para isso vamos usar a função `addPet` do contrato.

1. Ainda em `src/js/app.js`, substitua o bloco de comentário pelo seguinte código:

```javascript
event.stopPropagation();
var petName = $('#petName').val();

var adoptionInstance;
App.web3.eth.getAccounts(function(error, accounts) {
  if (error) {
    console.log(error);
  }

  var account = accounts[0];

  App.contracts.Adoption.deployed().then(function(instance) {
    adoptionInstance = instance;

    return adoptionInstance.addPet(petName, { from: account });
  }).then(function(result) {
    $('#addPetModal').modal('hide');
    setTimeout(function() {
      return App.loadPets();
    }, 3000);
  }).catch(function(err) {
    console.log(err.message);
  });
});
```

Observações:
- Essa operação irá escrever no nosso smart contract, logo é necessário enviar uma transação completa e assinada. Para isso precisamos da chave privada e de Ether para pagar o gas. Com o MetaMask, não precisamos lidar diretamente com a chave privada do usuário, basta informar qual o endereço a ser utilizado pela transação e internamente a extensão cuidará da assinatura.
- `web3.eth.getAccounts` retorna as contas presentes no web3. Como dito acima, o MetaMask seta automaticamente a conta selecionada.
- `adoptionInstance.addPet(petName, { from: account })` chama a função do smart contract, assinando com o endereço `from`.
- Observe que no retorno, utilizamos um `setTimeout`. Ele foi utilizado com propósito didático para simular o tempo de resposta da blockchain. 


### Adotando um pet

1. Novamente em `src/js/app.js`, substitua o bloco de comentário pelo seguinte código:

```javascript
event.preventDefault();

var petId = parseInt($(event.target).data('id'));

App.web3.eth.getAccounts(function(error, accounts) {
  if (error) {
    console.log(error);
  }

  var account = accounts[0];

  App.contracts.Adoption.deployed().then(function(instance) {
    adoptionInstance = instance;

    return adoptionInstance.adopt(petId, {from: account});
  }).then(function(result) {
    setTimeout(function() {
      return App.loadPets();
    }, 2000);
  }).catch(function(err) {
    console.log(err.message);
  });
});
```

Ok! O seu frontend já está pronto para ser utilizado.

## Interagindo com o dapp no browser

Agora estmoas prontos para usar o dapp!

### Instalando e configurando o MetaMask

O jeito mais fácil de interagir com o nosso dapp no browser é utilizando a extensão [MetaMask](https://metamask.io).

1. Instale o MetaMask no seu browser
2. Após finalizar a instalação, você verá o ícone da raposa próximo a barra de endereço. Clique nele e clique em "continue"
3. Clique em "import with seed phrase"
4. Vá até o ganache e copie o mnemonico e cole no campo de "seed phrase"

```
WARNING:
Não use esse mnemônico na mainnet. Se você enviar ETH para qualquer conta criada com esse mnemônico você provavelmente irá perder tudo!
```

5. Crie uma nova senha
6. Clique em `Accept` para aceitar a *Privacy Notice* e os demais termos requeridos.
7. Agora precisamos conectar o MetaMask na blockchain do Ganache. Clique no menu que mostra `Main Network` e selecione **Custom RPC**.
8. Na caixa de texto chamada "New RPC URL" digite `http://localhost:7545`.

O nome da network no topo irá mudar para "Private Network".

9. Clique na seta para retornar para a página de "Accounts".

Cada carteira criada com o comando Truffle Develop possui 100 ether. Você irá perceber um pouco menos do que isso na primeira conta, porque para fazer o deploy do contrato e rodar os testes, um pouco de ether foi gasto.

A configuração está completa agora!

## Iniciando o servidor http local

O repositório está configurado com o pacote `lite-server` que irá iniciar um servidor HTTP estático em `localhost:3000`.

Para iniciá-lo utilize o comando `npm run dev`.

## Usando o dapp
1. Rode o servidor local com `npm run dev`.

O servior irá iniciar e abrir automaticamente uma nova tab no seu browser com o dapp.

2. Clique em **add one**, coloque o nome do novo pet e clique no botão **Add pet**

3. Automaticamente, o MetaMask irá abrir um popup contendo informações sobre a transação a ser feita. Clique em **Submit** para aprovar a transação.

4. Após isso, você verá a transação listada na sua conta do MetaMask

Pronto! Agora que você viu como tudo funciona na prática, vamos propor um desafio.

## Parte final

Agora que você já entendeu a dinâmica do desenvolvimento para Ethereum, antes de atualizar o seu LinkedIn, vamos aprofundar um pouco mais o conhecimento com alguns desafios.

### Desafio 1
Implemente a opção de adicionar uma doação opcional ao adotar o pet.

Cuidar de animais não é barato... há gastos com veterinário, comida, etc. Por isso, seria interessante adicionar a opção de incluir um valor em Ether ao realizar a adoção.

Funções envolvendo transferência de Ether são muito importantes, por isso, tente criar essa nova funcionalidade!

>Dica: estude o modificador ```payable```

### Desafio 2
Implemente a funcionalidade de aceitar/recusar a adoção de um pet.

Essa atividade é um pouco mais desafiadora! Será necessário implementar um controle de estados para cada pet.

O fluxo deve ser o seguinte:

1. O usuário vai solicitar a adoção e incluir um valor em Ether (pode ser 0)
2. O dono do petshop vai aceitar a adoção e retirar o valor em Ether ou recusar a adoção e devolver o valor.

Observações:
- Um pet não pode ser adotado duas vezes.
- Somente o dono do petshop pode adicionar pet e aceitar/recusar as adoções.

>Dica: estude a definição de uma lista enumerável ```enum```

### Desafio 3
Implemente um log de adoções no smart contract. Toda vez que um pet for adotado emita um evento que poderá ser lido no frontend.

Bônus:
- Implemente uma página de log no frontend. 

> Dica: estude a criação de um ```event```
