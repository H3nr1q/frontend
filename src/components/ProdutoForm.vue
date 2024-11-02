<!-- src/components/ProdutoForm.vue -->

<template>
  <form @submit.prevent="submit">
    <div class="mb-3">
      <label for="descricao" class="form-label">Descrição</label>
      <input type="text" id="descricao" v-model="produto.descricao" class="form-control" required>
    </div>
    <div class="mb-3">
      <label for="custo" class="form-label">Custo</label>
      <input type="number" id="custo" v-model="produto.custo" class="form-control" required>
    </div>
    <div class="mb-3">
      <label for="imagem" class="form-label">Imagem</label>
      <input type="file" id="imagem" @change="onFileChange" class="form-control" accept="image/*">
    </div>

    <!-- Tabela de Preços de Venda -->
    <h5 class="mt-4">Preços de Venda por Loja</h5>
    <table class="table">
      <thead>
        <tr>
          <th>Loja</th>
          <th>Preço de Venda (R$)</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="preco in produto.precos" :key="preco.lojaId">
          <td>{{ preco.lojaDescricao }}</td>
          <td>
            <input type="number" v-model="preco.precoVenda" class="form-control" required>
          </td>
          <td>
            <button type="button" class="btn btn-danger" @click="removerPreco(preco.lojaId)">Excluir</button>
          </td>
        </tr>
        <tr>
          <td>
            <select v-model="novoPreco.lojaId" class="form-control">
              <option disabled value="">Selecione uma loja</option>
              <option v-for="loja in lojas" :key="loja.id" :value="loja.id">
                {{ loja.id }}-{{ loja.descricao }}
              </option>
            </select>
          </td>
          <td>
            <input type="number" v-model="novoPreco.precoVenda" class="form-control" placeholder="Preço de venda">
          </td>
          <td>
            <button type="button" class="btn btn-success" @click="adicionarPreco">Adicionar</button>
          </td>
        </tr>
      </tbody>
    </table>

    <button type="submit" class="btn btn-primary">Salvar Produto</button>
  </form>
</template>

<script>
export default {
  props: ['produtoData'],
  data() {
    return {
      produto: {
        descricao: '',
        custo: '',
        precos: [],
        imagem: null
      },
      lojas: [],  // Lista de lojas disponíveis para seleção
      novoPreco: { lojaId: '', precoVenda: '' }  // Dados temporários para um novo preço
    };
  },
   watch: {
    produtoData: {
      immediate: true,
      handler(newData) {
        if (newData) {
          this.produto = { ...newData };
        }
      }
    }
  },
  methods: {
    async onFileChange(event) {
      this.produto.imagem = event.target.files[0];
    },
    async carregarLojas() {
      try {
        const response = await this.$axios.get('/lojas');
        this.lojas = response.data;
      } catch (error) {
        console.error('Erro ao carregar lojas:', error);
      }
    },
    adicionarPreco() {
      const loja = this.lojas.find(loja => loja.id === this.novoPreco.lojaId);
      if (!loja || !this.novoPreco.precoVenda) {
        return alert('Selecione uma loja e insira um preço de venda válido.');
      }
      
      // Adiciona o preço de venda ao produto
      this.produto.precos.push({
        lojaId: this.novoPreco.lojaId,
        lojaDescricao: loja.descricao,
        precoVenda: this.novoPreco.precoVenda
      });
      
      // Limpa o formulário temporário
      this.novoPreco = { lojaId: '', precoVenda: '' };
    },
    removerPreco(lojaId) {
      this.produto.precos = this.produto.precos.filter(preco => preco.lojaId !== lojaId);
    },
    async submit() {
      const formData = new FormData();
      formData.append('descricao', this.produto.descricao);
      formData.append('custo', this.produto.custo);
      if (this.produto.imagem) formData.append('imagem', this.produto.imagem);
      // formData.append('precos', JSON.stringify(this.produto.precos));
      
      try {
        const response = this.produtoData
        ? await this.$axios.put(`/produto/${this.produtoData.id}`, formData)
        : await this.$axios.post('/produto', formData);
        
        this.$emit('produtoSalvo', response.data);
      } catch (error) {
        console.error('Erro ao salvar produto:', error);
      }
    }
  },
  async created() {
    await this.carregarLojas();
  }
};
</script>
