# Projeto Duelo Pokémon (NFT Game)
Construído na rede Mumbai
Compilado com Solidity 0.8.19

Site disponível em [https://nft-game.jg-lembo.repl.co/](https://nft-game.jg-lembo.repl.co/) através do Replit.

## A Plataforma

A plataforma do projeto tem como funcionalidade permitir que o usuário jogue o jogo Duelo Pokémon, comunicando suas ações para o contrato através dela. O jogo consiste de uma batalha dos personagens dos usuários contra um chefão, o "Boss".

Inicialmente, o usuário deve conectar sua carteira para poder participar do jogo. Depois, caso ainda não tenha um personagem (que, para o jogo, é uma NFT de um pokémon), o usuário poderá escolher um para si, cada um com diferentes atributos e habilidades.

Tendo um personagem, o usuário se dirige à tela em que tudo acontece, a arena, onde ele pode executar todas as ações disponíveis.

As ações disponíveis são:
- Atacar o Boss: O usuário usa seu personagem para atacar o Boss, aplicando o dano de ataque do personagem. O Boss ataca de volta, aplicando 50 de dano ao personagem do usuário. (Só disponível se o usuário tem mais de 0 de HP.)
- Curar o personagem: O usuário pode curar seu personagem, quando este está sem HP, para recuperar totalmente a sua vida. Para isso, entretanto, o personagem deve estar sem HP há mais de 8 horas.
- Usar habilidade especial: O usuário pode utilizar a habilidade especial de seu personagem, que é diferente para cada um dos pokémons. Essa ação só pode ser feita uma vez por hora.
- Ver os treinadores: O usuário consegue ver uma lista de todos os jogadores, com o endereço de suas carteiras, ID de seus tokens, dano causado e a imagem do pokémon daquele jogador.

Outras features:
- Dano crítico: Os personagens tem 10% de chance de causar o dobro de dano no seu ataque. Essa chance é calculada aleatoriamente pelo contrato.
- Condições: O jogo possui 3 condições, que são manipuladas pelas habilidades especiais dos personagens, sendo duas delas do Boss e uma dela dos jogadores. Elas são "Boss queimado", "Boss congelado" e "jogadores protegidos".
    - Boss queimado: O Boss sofre o dobro de dano por uma hora.
    - Boss congelado: O Boss sofre metade de dano mas tem apenas 10% de chance de causar dano, por 5 minutos.
    - Jogadores protegidos: Os jogadores recebem apenas 50% do dano aplicado pelo Boss, por uma hora.
- Habilidades especiais: Os jogadores podem utilizar habilidades especiais dos seus personagens, que são:
    - Charizard: Charizard ataca causando seu dano de ataque e QUEIMA o Boss, fazendo com que os ataques de todos os pokémons causem dano dobrado por uma hora.
    - Blastoise: Blastoise ataca causando seu dano de ataque e CONGELA o Boss, fazendo com que ele sofra metade do dano durante 5 minutos, mas também com que seus ataques tenham 90% de chance de não causar dano nos pokémons aliados.
    - Venusaur: Venusaur cria uma rede de plantas curativas fazendo com que todos os pokémons aliados sofram apenas 50% do dano recebido por uma hora.

## O Contrato

O contrato contém o código necessário para executar todas as funcionalidades do jogo, como cunhar NFTs e realizar as ações de personagem (Ataque, Habilidade Especial, Cura, etc).

O construtor do contrato é responsável por criar o Boss com seus atributos e o pool de personagens disponíveis para escolha dos jogadores.

A função _mintCharacterNFT_ é a responsável por criar o personagem de cada jogador. Ela utiliza o pool de personagens disponíveis para pegar o personagem escolhido e criar uma NFT para o usuário que o escolheu. A função _tokenURI_ fornece a NFT propriamente dita com todos os dados (imagem, nome, atributos, etc).

Depois temos as três funções responsáveis pelas ações principais do jogo, _attackBoss_, _useSpecialAbility_ e _healCharacter_.

- A função _attackBoss_ executa a ação básica de atacar o Boss. Primeiro, ela checa as condições necessárias para o ataque poder ocorrer (nesse caso, personagem e Boss com mais de 0 de vida), e depois as condições do jogo em si, como verificar se o Boss está queimado ou congelado, se os jogadores estão protegidos, chance de crítico, etc. Então, ela aplica os danos calculados no personagem e no Boss.
- A função _useSpecialAbility_ executa a habilidade especial do personagem. Primeiro, ela checa as condições necessárias para o personagem utilizar a habilidade (nesse caso, o personagem e o Boss com mais de 0 de vida e o personagem não ter usado a habilidade há menos de uma hora), e então utiliza a habilidade do personagem daquele usuário.
- A função _healCharacter_ executa a ação de curar o personagem do usuário. Primeiro, ela checa as condições necessárias para o usuário ser curado (nesse caso, a vida do personagem deve ser 0 e ele deve ter perdido seu HP há mais de 8 horas), e então restaura totalmente a vida do personagem.

As outras funções do contrato são auxiliares, e suas ações serão explicadas a seguir:
- A função _applyDamageConditions_ serve para verificar se o dano deve ser alterado pelas condições de jogo, isso é, dobrando o dano quando o Boss está queimado e diminuindo-o pela metade quando ele está congelado.
- A função _dealBossDamage_ diminui a vida do Boss de acordo com o dano passado como parâmetro, registrando esse dano para o personagem atacante, para que seja possível armazenar o total de dano causado por cada personagem.
- A função _checkIfUserHasNFT_ serve para verificar se o usuário possui uma NFT, assim a plataforma sabe que deve redirecionar o usuário para a página da Arena.
- A função _getTimeSinceDefeat_ tem como objetivo fornecer a quanto tempo o personagem teve seu HP reduzido a 0. Assim, o frontend pode saber quanto tempo falta para o personagem poder se curar. A função _getTimeSinceSpecialAbilityUse_ tem o mesmo objetivo, mas calculando o tempo desde o último uso da habilidade especial, para saber quando ela pode ser usada novamente.
- A função _updateConditions_ é utilizada para atualizar as três condições do jogo quando necessário. Isso é, ela verifica se o tempo de uma condição já passou (por exemplo, o Boss foi queimado há mais de uma hora) e então a atualiza (no exemplo, "setando" que o Boss não está queimado).
- A função _getAllPlayers_ retorna os jogadores, com suas carteiras, IDs de token, dano causado e ícone do personagem, assim o frontend pode exibir uma tabela com todos os jogadores.
- A função _getAllDefaultCharacters_ retorna os personagens disponíveis, para que o usuário possa escolher qual quer mintar, e a função _getBigBoss_ retorna o Boss para ser exibido na plataforma.