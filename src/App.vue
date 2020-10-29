<template>
  <div>
    <b-navbar type="dark" variant="dark">
      <b-navbar-nav>
        <b-nav-item v-on:click="mostrarCart = true">Home</b-nav-item>

        <!-- Navbar dropdowns -->
        <b-nav-item v-on:click="mostrarCart = false">Carrinho</b-nav-item>

        <b-nav-item-dropdown text="User" right>
          <b-dropdown-item href="#">Account</b-dropdown-item>
          <b-dropdown-item href="#">Settings</b-dropdown-item>
        </b-nav-item-dropdown>
      </b-navbar-nav>
    </b-navbar>

    <b-container>

      <div class="cumprimento">
        <h2 v-if="ehManha">Bom dia!</h2>
        <h2 v-if="ehTarde">Boa tarde!</h2>
        <h2 v-if="ehNoite">Boa noite!</h2>

        <h2>Seja bem vindo à locadora de filmes MovieTime!</h2>
      </div>

      <HelloWorld msg="" />

      <div v-show="mostrarCart" class="cards">
        <b-card v-for="filme in filmes" :key="filme.id" :img-src=filme.imagem img-alt="Image"
          img-top tag="article" style="max-width: 20rem;" class="mb-2">

          <b-card-title>
            {{filme.titulo}} ({{filme.ano}})
          </b-card-title>

          <b-card-text >
            {{filme.showDescricao}}
          </b-card-text>

          <b-button v-on:click="readMore(filme.showDescricao)" id="btnRead" variant="secondary" size="sm">Ler mais</b-button>
          <br>
          <br>
          <b-button v-if="filme.qtdEstoque > 0" v-on:click="adicionarAoCarrinho(filme)" id="btnRent" href="#"
            variant="outline-success">Alugar por {{formatReal(filme.valor)}}</b-button>
          <!-- <b-button v-else-if="filme.qtdEstoque == 1" variant="warning"> ÚLTIMA UNIDADE!</b-button> -->
          <!-- <b-button v-else-if="filme.qtdEstoque == 1" variant="warning" @click="adicionarAoCarrinho(filme)">ÚLTIMA UNIDADE</b-button> -->


          <b-button v-else href="#" block variant="danger" disabled> ESGOTADO</b-button>

        </b-card>
      </div>

      <div v-if="!mostrarCart" class="card bg-light mb-3" style="max-width: 20rem;">
        <div class="card-header">Resumo de compra:</div>


        <b-table striped hover :items="carrinho" :fields="fields"></b-table>

        <div class="card-body">
          <p class="card-text">Total de <b>{{ quantidadeNoCarrinho }}</b> filme(s) alugado(s).</p>
          <h5 class="card-title">{{ formatReal(valortotal) }}</h5>

        </div>
      </div>

    </b-container>
  </div>

</template>

<script>
  import 'bootstrap/dist/css/bootstrap.css'
  import 'bootstrap-vue/dist/bootstrap-vue.css'

  import HelloWorld from './components/HelloWorld.vue'

  var horanow = new Date().getHours()

export default {
  name: 'App',
  components: {
    HelloWorld
  } , data(){
    return { 
      title : "Locadora de Filmes",
      ehManha: horanow <= 12,
      ehTarde: horanow < 18 && horanow > 12,
      ehNoite: horanow >= 18,
      fields: ["titulo", "preco", `qtdComprados`],
      filmes : [
        { id: 1, titulo: "O Incrível Hulk", ano: 2008, qtdEstoque: 5,  descricao: "Vivendo escondido e longe de Betty Ross (Liv Tyler), a mulher que ama, o cientista Bruce Banner (Edward Norton) busca um meio de retirar a radiação gama que está em seu sangue. Ao mesmo tempo ele precisa fugir da perseguição do general Ross (William Hurt), seu grande inimigo, e da máquina militar que tenta capturá-lo, na intenção de explorar o poder que faz com que Banner se transforme no Hulk.", valor: 2, imagem: "https://i.imgur.com/0uthCmp.jpg" },
        { id: 2, titulo: "Homem de Ferro 2", ano: 2010, qtdEstoque: 2,  descricao: "Após confessar ao mundo ser o Homem de Ferro, Tony Stark (Robert Downey Jr.) passa a ser alvo do governo dos Estados Unidos, que deseja que ele entregue seu poderoso traje. Com a negativa, o governo passa a desenvolver um novo traje com o maior rival de Stark, Justin Hammer (Sam Rockwell). Jim Rhodes (Don Cheadle), o braço direito de Tony, é colocado no centro deste conflito, o que faz com que assuma a identidade de Máquina de Combate. Paralelamente, Ivan Vanko (Mickey Rourke) cria o alter-ego de Whiplash para se vingar dos atos da família Stark no passado. Para combater Whiplash e a perseguição do governo, Stark conta com o apoio de sua nova assistente, Natasha Romanoff (Scarlett Johansson), e de Nick Fury (Samuel L. Jackson), o diretor da S.H.I.E.L.D.", valor: 10.28, imagem: "https://i.imgur.com/OA8pDFM.jpg" },
        { id: 3, titulo: "Thor", ano: 2011, qtdEstoque: 4,  descricao: "Thor (Chris Hemsworth) estava prestes a receber o comando de Asgard das mãos de seu pai Odin (Anthony Hopkins) quando forças inimigas quebraram um acordo de paz. Disposto a se vingar do ocorrido, o jovem guerreiro desobedece as ordens do rei e quase dá início a uma nova guerra entre os reinos. Enfurecido com a atitude do filho e herdeiro, Odin retira seus poderes e o expulsa para a Terra. Lá, Thor acaba conhecendo a cientista Jane Foster (Natalie Portman) e precisa recuperar seu martelo, enquanto seu irmão Loki (Tom Hiddleston) elabora um plano para assumir o poder. Mas os guerreiros do Deus do Trovão fazem a mesma viagem para buscar o amigo e impedir que isso aconteça. Só que eles não vieram sozinhos e o inimigo está presente para uma batalha que está apenas começando. ", valor: 20.56, imagem: "https://i.imgur.com/mt4ZRzw.jpg" },
        { id: 4, titulo: "Capitão América: O Primeiro Vingador", qtdEstoque: 1,  ano: 2011,descricao: "2ª Guerra Mundial. Steve Rogers (Chris Evans) é um jovem que aceitou ser voluntário em uma série de experiências que visam criar o supersoldado americano. Os militares conseguem transformá-lo em uma arma humana, mas logo percebem que o supersoldado é valioso demais para pôr em risco na luta contra os nazistas. Desta forma, Rogers é usado como uma celebridade do exército, marcando presença em paradas realizadas pela Europa no intuito de levantar a estima dos combatentes. Para tanto passa a usar uma vestimenta com as cores da bandeira dos Estados Unidos, azul, branca e vermelha. Só que um plano nazista faz com que Rogers entre em ação e assuma a alcunha de Capitão América, usando seus dons para combatê-los em plenas trincheiras da guerra.", valor: 40.00, imagem: "https://i.imgur.com/UFmSVtZ.jpg" },
        { id: 5, titulo: "Doutor Estranho", ano: 2016, qtdEstoque: 7, descricao: "Stephen Strange (Benedict Cumberbatch) leva uma vida bem sucedida como neurocirurgião. Sua vida muda completamente quando sofre um acidente de carro e fica com as mãos debilitadas. Devido a falhas da medicina tradicional, ele parte para um lugar inesperado em busca de cura e esperança, um misterioso enclave chamado Kamar-Taj, localizado em Katmandu. Lá descobre que o local não é apenas um centro medicinal, mas também a linha de frente contra forças malignas místicas que desejam destruir nossa realidade. Ele passa a treinar e adquire poderes mágicos, mas precisa decidir se vai voltar para sua vida comum ou defender o mundo.", valor: 10.50, imagem: "https://i.imgur.com/pVEDruM.jpg" },
        { id: 6, titulo: "Pantera Negra", ano:2018, qtdEstoque: 3,  descricao: "Em Pantera Negra, após a morte do rei T'Chaka (John Kani), o príncipe T'Challa (Chadwick Boseman) retorna a Wakanda para a cerimônia de coroação. Nela são reunidas as cinco tribos que compõem o reino, sendo que uma delas, os Jabari, não apoia o atual governo. T'Challa logo recebe o apoio de Okoye (Danai Gurira), a chefe da guarda de Wakanda, da irmã Shuri (Letitia Wright), que coordena a área tecnológica do reino, e também de Nakia (Lupita Nyong'o), a grande paixão do atual Pantera Negra, que não quer se tornar rainha. Juntos, eles estão à procura de Ulysses Klaue (Andy Serkis), que roubou de Wakanda um punhado de vibranium, alguns anos atrás.", valor: 10.50, imagem: "https://i.imgur.com/JOSEGKf.jpg" }
      ],
      isCutted: true,
      carrinho: [],
      valortotal: 0,
      ids_cadastrados: [],
      mostrarCart: true
      }
      }, methods: {
      formatReal(n){
      var f = n.toLocaleString('pt-br',{style: 'currency', currency: 'BRL'});
      return f;
      },

      first_parcel(str){
      let string_sliced = str.slice(0, 150);
      return string_sliced;
      },

      last_parcel(str){
      let string_sliced = str.slice(150);
      return string_sliced;
      },

      readMore(filme){
        console.log('heei', filme.descricao)
      },

      adicionarAoCarrinho(filme){
      this.valortotal += filme.valor
      filme.qtdEstoque -= 1

      if(this.ids_cadastrados.includes(filme.id)){
      // filme.qtdComprados += 1
      filme.qtdComprados = (filme.qtdComprados || 0) +1
      }
      else {
      this.ids_cadastrados.push(filme.id)
      // filme.qtdComprados += 1

      //se não existir a variavel qtdComprados, ele cria com valor 0 e adiciona 1
      filme.qtdComprados = (filme.qtdComprados || 0) +1

      filme.preco = `${this.formatReal(filme.valor)}`
      this.carrinho.push(filme)
      }
      },
      },

      mostrarCarrinho(){
      return this.mostrarCart = false
      },

      computed: {
      quantidadeNoCarrinho: function(){
      return this.carrinho.length
      }
      }, created(){
        for (let filme of this.filmes){
          filme.showDescricao = this.first_parcel(filme.descricao)
        }`
      `}
      }

      </script>

      <style>
        body {
          background-color: rgba(61, 61, 61, 0.192);
        }

        .row {
          justify-content: center;
        }

        .cards {
          display: flex;
          flex-wrap: wrap;
          justify-content: space-around;
          text-align: justify;
        }

        .card.bg-light.mb-3 {
          margin: 0 auto;
        }

        #more {
          display: none;
        }

        a#btnRent {
          width: 100%;
        }

        .cuttext {
          text-overflow: ellipsis;
          overflow: hidden;
          width: 17rem;
          height: 60px;
          white-space: nowrap;
        }

        .cumprimento {
          text-align: center;
        }
      </style>
