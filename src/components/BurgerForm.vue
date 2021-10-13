<template>
    <div>
        <Messege :msg="msg" v-show="msg"/>
        <div>
            <form id="burger-form" @submit="createBurger">

                <div class="input-container">
                    <label for="nome">Nome do Cliente:</label>
                    <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite seu nome">
                </div>

                <div class="input-container">
                    <label for="fone">Telefone :</label>
                    <input type="text" id="fone" name="fone" v-model="fone" placeholder="Digite seu telefone">
                </div>

                <div class="input-container">
                   <label for="pao">Escolha o Pão:</label>

                   <select name="pao" id="pao" v-model="pao">

                       <option value="">Selecione seu pão</option>
                       <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
                           {{pao.tipo}}
                        </option>

                   </select>
                </div>

                <div class="input-container">
                   <label for="carne">Escolha a Carne do sue Burger:</label>

                   <select name="carne" id="carne" v-model="carne">

                       <option value="">Selecione sua Carne</option>
                       <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
                           {{carne.tipo}}
                        </option>

                   </select>
                </div>

                <div id="opcionais-container" class="input-container">
                    <label id="opcionais-title" for="opcionais">Selecione Opcionais:</label>
                    <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
                        <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                        <span>{{opcional.tipo}}</span>

                    </div>  
                </div>

                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar meu Burger!">
                </div>   
                
            </form>
        </div>
    </div>
    
</template>

<script>
import Messege from './Message.vue'

export default {
    name:'BurgerForm',
    data(){
        return{
            paes:null,
            carnes:null,
            opcionaisdata:null,
            nome:null,
            fone:null,
            pao:null,
            carne:null,
            opcionais:[],
            msg:null
        }
    },
    methods:{
        async getIngredientes(){

            const req = await fetch("http://localhost:3000/ingredientes");
            const data = await req.json();

            this.paes = data.paes;
            this.carnes = data.carnes;
            this.opcionaisdata = data.opcionais;

        },
        async createBurger(e){

            e.preventDefault();

            const data ={
                nome: this.nome,
                fone: this.fone,
                carne: this.carne,
                pao: this.pao,
                opcionais: Array.from(this.opcionais),
                status: "Solicitado",
            }

            const dataJson = JSON.stringify(data);

            const req = await fetch("http://localhost:3000/burgers",{
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: dataJson
            });

            const res =await req.json();

            //colocar uma mensagem de sistema
            this.msg=`Pedido N° ${res.id} realizado com sucesso!`

            //limpar msg
            setTimeout(()=> this.msg = "", 3000 );

            //limpar os campos
            this.nome="";
            this.fone="",
            this.carne="";
            this.pao="";
            this.opcionais="";
        }
    },
    mounted(){
        this.getIngredientes()
    },
    components:{
        Messege
    }
}
</script>

<style scoped>
    #burger-form{
        max-width: 400px;
        margin: 0 auto;
    }

    .input-container{
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label{
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #fcba03;
    }

    input, select{
        padding: 5px 10px;
        widows: 300px;
    }

    #opcionais-container{
        flex-direction: row;
        flex-wrap: wrap;
    }

    #opcionais-title{
        width: 100%;
    }

    .checkbox-container{
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }

    .checkbox-container span,
    .checkbox-container input{
        width: auto;
    }

    .checkbox-container span{
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn{
        background-color: #222;
        color: #fcba03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }

    .submit-btn:hover{
        background-color: transparent;
        color: #222;
    }
</style>