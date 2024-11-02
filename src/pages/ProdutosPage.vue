<template>
  <div class="container mt-5">
    <router-link to="/produto/cadastro" class="btn btn-primary mb-3">Novo Produto</router-link>
    <ProdutoList 
      :produtos="produtos" 
      @editarProduto="abrirModalEdicao" 
      @excluirProduto="excluirProduto" />
    <ProdutoModal ref="modal" 
      @produtoSalvo="carregarProdutos" />
  </div>
</template>

<script>
import ProdutoList from '@/components/ProdutoList.vue';
import ProdutoModal from '@/components/ProdutoModal.vue';
import { useToast } from 'vue-toastification';

export default {
  components: { ProdutoList, ProdutoModal },
  data() {
    return { 
      produtos: [] 
    };
  },
  methods: {
    async carregarProdutos() {
      try {
        const response = await this.$axios.get('/produtos');
        this.produtos = response.data;
      } catch (error) {
        console.error('Erro ao carregar produtos:', error);
      }
    },
  //   async abrirModalEdicao(produto) {
  //   try {
  //     const response = await this.$axios.get(`/produtolojas/${produto.id}`);
  //     const dados = response.data;
  //     // Estrutura o objeto do produto para incluir um array de precos
  //     const produtoComPrecos = {
  //       ...dados[0].produto,  // Pega os detalhes do produto
  //       precos: dados.map(item => ({
  //         lojaId: item.loja.id,
  //         lojaDescricao: item.loja.descricao,
  //         precoVenda: item.preco_venda
  //       }))
  //     };
  //     this.$refs.modal.open(produtoComPrecos);  // Passa o objeto estruturado para o modal
  //   } catch (error) {
  //     console.error('Erro ao carregar produto:', error);
  //   }
  // },
    async abrirModalEdicao(produto) {
      try {
        // Aqui, se você quiser buscar mais dados do produto, pode fazer isso
        this.$router.push({ name: 'EditarProduto', params: { id: produto.id } });
      } catch (error) {
        console.error('Erro ao abrir edição do produto:', error);
      }
    },
    async excluirProduto(produto) {
      try {
        await this.$axios.delete(`/produto/${produto.id}`);
        this.carregarProdutos();
        this.$toast.success('Produto deletado com sucesso!');
      } catch (error) {
        console.error('Erro ao excluir produto:', error);
        this.$toast.error(`Erro ao excluir produto:${error}`);
      }
    }
  },
  mounted() {
    this.carregarProdutos();
  }
};
</script>
