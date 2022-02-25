<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <div>
      <!--INICIO DO FORMULÁRIO-->
      <form id="burger-form" @submit="createBurger">
        <div class="input-container">
          <label for="nome">Nome do Cliente</label>
          <input
            type="text"
            id="nome"
            v-model="nome"
            placeholder="Digite seu nome"
          />
        </div>
        <div class="input-container">
          <label for="pao">Escolha o pão:</label>
          <select name="pao" id="pao" v-model="pao">
            <option value="">Selecione o seu pão</option>
            <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
              {{ pao.tipo }}
            </option>
          </select>
        </div>
        <div class="input-container">
          <label for="carne">Escolha a carne do seu burger:</label>
          <select name="carne" id="carne" v-model="carne">
            <option value="">Selecione a carne:</option>
            <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
              {{ carne.tipo }}
            </option>
          </select>
        </div>
        <div class="input-container">
          <label id="opcionais-title" for="opcionais"
            >Selecione os opcionais:</label
          >
          <div
            class="checkbox-container"
            v-for="opcional in opcionaisdata"
            :key="opcional.id"
          >
            <input
              type="checkbox"
              name="opcionais"
              v-model="opcionais"
              :value="opcional.tipo"
            />
            <span>{{ opcional.tipo }}</span>
          </div>
        </div>
        <div class="input-container">
          <input type="submit" class="submit-btn" value="Criar meu burger!" />
        </div>
      </form>
      <!--FINAL DO FORMULÁRIO-->
    </div>
  </div>
</template>

<script>
import Message from './Message.vue';
export default {
  name: "BurgerForm",
  data() {
    return {
      paes: null,
      carnes: null,
      opcionaisdata: null,
      nome: null,
      pao: null,
      carne: null,
      opcionais: [],
      msg: null,
    };
  },
  methods: {
    // MÉTODO QUE BUSCA OS DADOS DO BACKEND PARA OS FORMULARIOS
    async getIngredientes() {
      const req = await fetch("http://localhost:3000/ingredientes");
      const data = await req.json();

      this.paes = data.paes;
      this.carnes = data.carnes;
      this.opcionaisdata = data.opcionais;
    },
    // FUNÇÃO QUE IRÁ CRIAR O BURGER
    // LINKAR NO FORMULARIO @submit="createBurger"
    async createBurger(e) {
      e.preventDefault();
      // criamos um obj que irá buscar os dados preenchidos do formulário.
      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: Array.from(this.opcionais),
        status: "Solicitado",
      };

      // Agora transformamos o obj em uma string para poder enviar
      const dataJson = JSON.stringify(data);

      // Agora efetuamos a requisição
      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type": "application/json" }, //comunicando com json
        body: dataJson, // corpo envia como json
      });

      // RESPOSTA
      const res = await req.json();

      // colocar uma msg do sistema
      this.msg = `Pedido Nº ${res.id}, realizado com sucesso!`

      // limpar msg
      setTimeout(() => {
        this.msg = ""
      }, 5000);

      // limpar os campos após concluído.
      this.nome = "";
      this.carne = "";
      this.pao = "";
      this.opcionais = "";
    },
  },
  // QUANDO MONTAR O FORMULARIO JÁ BUSCAR OS DADOS DO SERVIDOR, PARA OS CAMPOS
  mounted() {
    this.getIngredientes();
  },
  components:{
    Message
  }
};
</script>

<style scoped>
#burger-form {
  max-width: 400px;
  margin: 0 auto;
}

.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}

input,
select {
  padding: 5px 10px;
  widows: 300px;
}

#opcionais-container {
  flex-direction: row;
  flex-wrap: wrap;
}

#opcionais-title {
  width: 100%;
}

.checkbox-container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
}

.checkbox-container span {
  margin-left: 6px;
  font-weight: bold;
}

.submit-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border-bottom: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.3s;
}

.submit-btn:hover {
  background-color: transparent !important;
  color: #222;
}
</style>
