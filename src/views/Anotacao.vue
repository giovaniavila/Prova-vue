<template>
  <div>
    <p>texto: <input type="text" v-model="texto" required /></p>
    <p>data hora: <input type="datetime-local" v-model="dataHora" /></p>
    <p required>
      usuarios:
      <select name="usuarios" id="" v-model="idUsuario">
        <option v-for="(usuario, index) in usuarios" :key="index" :value="usuario.id">
          {{ usuario.nome }}
        </option>
      </select>
    </p>
    <p required>idUsario : <input type="number" v-model="idUsuario" /></p>
    <button @click="incluir">Cadastrar</button>
    <p v-if="erro">{{ erro }}</p>
    <table>
      <thead>
        <tr>
          <td>Id</td>
          <td>Nome</td>
          <td>Antiguidade</td>
          <td>Usuario</td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(anotacao, index) in anotacoes" :key="index">
          <td>{{ anotacao?.id }}</td>
          <td>{{ anotacao?.texto }}</td>
          <td>{{ calcularMesesDesde(anotacao?.dataHora ?? '') }} meses</td>
          <td>{{ anotacao?.usuario.nome }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import { onMounted } from 'vue'

const texto = ref<string>('')
const dataHora = ref<Date>()
const idUsuario = ref<number>()
const erro = ref<string>('')

const anotacoes = ref([
  {
    id: 1,
    texto: 'Meu novo projeto',
    dataHora: '2023-08-01T19:10:00',
    usuario: {
      id: 1,
      nome: 'admin',
      autorizacoes: [
        {
          id: 1,
          nome: 'ROLE_ADMIN',
        },
      ],
    },
  },
  ,
  {
    id: 2,
    texto: 'Meu novo projeto 23',
    dataHora: '2023-08-01T19:10:00',
    usuario: {
      id: 1,
      nome: 'admin',
      autorizacoes: [
        {
          id: 1,
          nome: 'ROLE_ADMIN',
        },
      ],
    },
  },
  ,
])

async function atualizar() {
  try {
    anotacoes.value = (await axios.get('anotacao')).data
    erro.value = ''
  } catch (e) {
    erro.value = (e as Error).message
  }
}

function calcularMesesDesde(dataHora: string): number {
  const dataInicial = new Date(dataHora)
  const dataAtual = new Date()

  const anoInicial = dataInicial.getFullYear()
  const mesInicial = dataInicial.getMonth()

  const anoAtual = dataAtual.getFullYear()
  const mesAtual = dataAtual.getMonth()

  return (anoAtual - anoInicial) * 12 + (mesAtual - mesInicial)
}

interface usuario {
  id?: number
  nome: string
  senha: string
}

const usuarios = ref<usuario[]>([
  { id: 1, nome: 'admin', senha: '123' },
  { id: 2, nome: 'teste', senha: 'abc' },
])

async function buscarUsuarios() {
  try {
    usuarios.value = (await axios.get('usuario')).data
    erro.value = ''
  } catch (e) {
    erro.value = (e as Error).message
  }
}

async function incluir() {
  if (!texto.value || !dataHora.value || !idUsuario.value) {
    erro.value = 'Preencha todos os campos obrigatórios.'
    return
  }

  try {
    await axios.post('anotacao', {
      texto: texto.value,
      dataHora: dataHora.value,
      usuario: {
        id: idUsuario.value,
      },
    })
    erro.value = ''
    texto.value = ''
    dataHora.value = undefined
    idUsuario.value = 0
    atualizar()
  } catch (e) {
    erro.value = (e as Error).message
  }
}

onMounted(() => {
  atualizar()
  buscarUsuarios()
})
</script>
