<template>
  <div>
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
          <td>{{ calcularMesesDesde(anotacao?.dataHora ?? "")}}</td>
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

const nome = ref<string>('')
const senha = ref<string>('')
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

async function incluir() {
  try {
    await axios.post('usuario', {
      nome: nome.value,
      senha: senha.value,
    })
    erro.value = ''
    nome.value = ''
    senha.value = ''
    atualizar()
  } catch (e) {
    erro.value = (e as Error).message
  }
}

onMounted(() => {
  atualizar()
})
</script>
