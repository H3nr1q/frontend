<template>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>ID</th>
        <th>Descrição</th>
        <th>Custo</th>
        <th>Ações</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="produto in produtos" :key="produto.id">
        <td>{{ produto.id }}</td>
        <td>{{ produto.descricao }}</td>
        <td>{{ produto.custo }}</td>
        <td>
          <button class="btn btn-primary me-2" @click="$emit('editarProduto', produto)">
            <i class="bi bi-pencil"></i>
          </button>
          <button class="btn btn-danger" @click="$emit('excluirProduto', produto)">
            <i class="bi bi-trash"></i>
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  props: ['produtos']
};
</script>
