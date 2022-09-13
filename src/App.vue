<template>
  <div id="app">
    
    <div class="urna"> 

      <TelaI 
      :tela="tela"
      :numeroVoto="numeroVoto"
      :quantidadeNumeros="quantidadeNumeros"
      :candidato="candidato"
      />
      <TecladoI 
        :adicionarNumero="adicionarNumero"
        :corrigir="corrigir"
        :confirmar="confirmar"
        :votarEmBranco="votarEmBranco"
      />

    </div>


  </div>
</template>

<script>

import '@/css/global.css';

import TecladoI from '@/components/TecladoI';
import TelaI from '@/components/TelaI';

import confirmAudio from '@/assets/audios/confirm.wav';
import keyAudio from '@/assets/audios/key.wav';

import fetch from 'cross-fetch';

export default {
  name: 'App',
  components: {
   TecladoI,
   TelaI
  },
  methods:{
    adicionarNumero(numero){
      //EXECUTA SOM DA TECLA
      this.executarSom(keyAudio);

      //VERIFICA LIMITE DE NUMEROS VOTADOS
      if(this.numeroVoto.length == this.quantidadeNumeros){
        return false;
      }

      //ADICIONA O NUMERO SELECIONADO
      this.numeroVoto += ''+numero;


      //VERIFICA CANDIDATO VOTADO
      this.verificarCandidato();
    },
    verificarCandidato(){
      //VOTO INCOMPLETO
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }

      //VERIFICA CANDIDATO EXISTENTE
      if(this.candidatos[this.tela][this.numeroVoto]){
        this.candidato = this.candidatos[this.tela][this.numeroVoto];
        return true;
      }

      //VOTO NULO
      this.candidato = {
        nome: 'Voto nulo',
        partido: 'Voto nulo',
        imagem: ''
      }
    },
    corrigir(){
      //EXECUTA SOM DA TECLA
      this.executarSom(keyAudio);
      
      this.limpar();
    },
    limpar(){
      this.candidato = {}
      this.numeroVoto = ''
    },
    confirmar() {
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }

      fetch(`https://bubble-voto-back.herokuapp.com/votar/presidente/${this.numeroVoto}`).then()
      return this.avancarTela();
    },
    avancarTela(){
      //EXECUTA SOM DE CONFIRMAÇAO
      this.executarSom(confirmAudio);

      //Tela de confirmação
      if(this.tela == 'presidente'){
        this.tela = 'fim';
      }

      //INSTANCIA ATUAL
      var instancia = this;

      //VOLTAR AO INICIO
      setTimeout(function(){
        instancia.tela = 'presidente';
        instancia.quantidadeNumeros = 2;
        return instancia.limpar()
      },7000);
    },
    votarEmBranco(){
      //TELA DE FINALIZAÇÃO
      if(this.tela == 'fim') return false;

      this.limpar();
      this.avancarTela();
    },
    executarSom(arquivoSom){
      if(arquivoSom){
        let audio = new Audio(arquivoSom);
        audio.play();
      }
    }
  },

  data(){
    return {
      tela: 'presidente',
      numeroVoto:'',
      quantidadeNumeros: 2,
      candidato: {},
      candidatos: {
            "presidente":{
              "13":{
                "nome": "LULA",
                "partido": "PT",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/f/f4/Lula_-_foto_oficial_-_05_jan_2007.jpg/220px-Lula_-_foto_oficial_-_05_jan_2007.jpg"
              },
              "22":{
                "nome": "JAIR BOLSONARO",
                "partido": "PL",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/Presidente_Jair_M_Bolsonaro_%28cropped%29.jpg/200px-Presidente_Jair_M_Bolsonaro_%28cropped%29.jpg"
              },
              "12":{
                "nome": "CIRO GOMES",
                "partido": "PDT",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/e/e2/Ciro_Gomes_%28cropped%29.jpg/250px-Ciro_Gomes_%28cropped%29.jpg"
              },
              "15":{
                "nome": "SIMONE TEBET",
                "partido": "MDP",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/8/8f/Encontro_de_amigas_e_amigos_do_MDB_com_a_senadora_Simone_Tebet_%2852037060195%29_%28cropped%29_2.jpg/200px-Encontro_de_amigas_e_amigos_do_MDB_com_a_senadora_Simone_Tebet_%2852037060195%29_%28cropped%29_2.jpg"
              },
              "30":{
                "nome": "LUIZ FELIPE d´Avila",
                "partido": "NOVO",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/8/8c/Luiz-felipe-davila_retrato_%28cropped%29.jpg/200px-Luiz-felipe-davila_retrato_%28cropped%29.jpg"
              },
              "44":{
                "nome": "SORAYA THRONICKE",
                "partido": "UNIÃO",
                "imagem": "https://upload.wikimedia.org/wikipedia/commons/thumb/e/e1/Senadora_Soraya_Thronicke.jpg/200px-Senadora_Soraya_Thronicke.jpg"
              },
            },
}
    }
  }
}
</script>

<style>
#app {
 background-color: var(--background-color);
 width: 100%;
 height: 100%;
 display: flex;
 justify-content: center;
 align-items: center;
}

.urna{
  width: 1000px;
  height: 500px;
  background-color: var(--ballot-box-background-color);
  padding: 30px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
}
</style>
