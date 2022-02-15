<template>
  <section>
    <div class="urna">
      <div class="tela">
        <div v-if="count2 == null" class="numbersSelected">{{ count }}</div>
        <div v-else class="numbersSelected">{{ concat }}</div>
        <div v-if="politico && concat != null">
          <div v-if="loading == true" class="loading">
            <half-circle-spinner
              :animation-duration="1000"
              :size="60"
              color="#5e5b5b"
            />
          </div>
          <div v-else>
            <div v-if="politico.dados" class="politicoInfo">
              <img
                v-bind:src="politico.dados.ultimoStatus.urlFoto"
                :style="{ height: 200 + 'px', padding: 5 + 'px' }"
              />

              <div :style="{ display: 'flex', flexDirection: 'column' }">
                <h2>{{ politico.dados.nomeCivil }}</h2>
                <p>Nascimento: {{ politico.dados.dataNascimento }}</p>
                <p>Estado: {{ politico.dados.ultimoStatus.siglaUf }}</p>
                <p>Escolaridade: {{ politico.dados.escolaridade }}</p>
              </div>
            </div>
            <div v-else :style="{ display: 'flex', justifyContent: 'center' }">
              <h3>Candidato não encontrado</h3>
            </div>
          </div>
        </div>
      </div>
      <div class="teclado">
        <div v-if="count == null || count2 != null" class="numeros">
          <button
            v-for="n in 9"
            v-bind:key="n"
            @click="(count = n), (count2 = null)"
            class="btnUrna"
          >
            {{ n }}
          </button>

          <button @click="(count = 0), (count2 = null)" class="btnUrna">
            0
          </button>
        </div>
        <div v-if="count != null && count2 == null" class="numeros">
          <button
            v-for="n in 9"
            v-bind:key="n"
            @click="(count2 = n), setChoice()"
            class="btnUrna"
          >
            {{ n }}
          </button>

          <button @click="(count2 = 0), setChoice()" class="btnUrna">0</button>
        </div>
        <div class="confirm">
          <button class="btnUrnaResolve">WHITE</button>
          <button
            @click="concat = null"
            class="btnUrnaResolve"
            style="background-color: orange"
          >
            CORRECT
          </button>
          <button class="btnUrnaResolve" style="background-color: #16a085">
            CONFIRM
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { HalfCircleSpinner } from "epic-spinners";
const API_URL = `https://dadosabertos.camara.leg.br/api/v2/deputados/`;

export default {
  components: {
    HalfCircleSpinner,
  },
  data() {
    return {
      count: null,
      count2: null,
      concat: null,
      choice: null,
      politico: null,
      loading: false,
    };
  },

  methods: {
    setChoice() {
      this.concat = `${this.count}${this.count2}`;
      this.choice = parseInt(this.concat);
      this.fetchData();
    },
    async fetchData() {
      this.loading = true;
      try {
        const url = `${API_URL}${this.choice}`;
        this.politico = await (await fetch(url)).json();
        this.loading = false;
      } catch (error) {
        console.log(error);
        this.loading = false;
      }
    },
  },
};
</script>

<style>
@import "../assets/urna.css";
</style>
