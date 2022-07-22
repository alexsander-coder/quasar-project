<template>
  <q-page padding>
    <q-table
      title="Treats"
      :rows="posts"
      :columns="columns"
      row-key="name"
      >
        <template v-slot:top>
          <span class="text-h5">Cadastro</span>
          <q-space />
          <q-btn color="primary" label="Novo" :to="{ name: 'formPost' }"/>
        </template>


       <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          <q-btn icon="delete" color="negative" dense size="sm" @click="handleDeletePost(props.row.id)"/>
        </q-td>
       </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const columns = [ 
  { name: 'id', field: 'id', label: 'id', sortable: true, align: 'left' },
  { name: 'title', field: 'title', label: 'titulo', sortable: true, align: 'left' },
  { name: 'author', field: 'author', label: 'autor', sortable: true, align: 'left' },
  { name: 'actions', field: 'actions', label: 'ações', align: 'right' }
]
const $q = useQuasar()

onMounted(() => {
  getPosts()
})

  const getPosts = async () => {
    try {
      const data = await list('posts')
      posts.value = data
    } catch (error) {
      
    }
  }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
        title: 'Remover',
        message: 'Deseja remover este post?',
        cancel: true,
        persistent: true
      }).onOk(async () => {
         await remove(id)
        $q.notify({ message: 'Apagado com sucesso', icon: 'check', color: 'positive'})
        await getPosts()
      })
      } catch (error) {
        $q.notify({ message: 'Erro ao apagar', icon: 'times', color: 'negative'})
      }
    }

    return {
      posts,
      columns,
      handleDeletePost
    }
  }
})
</script>
