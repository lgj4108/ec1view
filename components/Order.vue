<template>
  <div>
    <b-container>
      <b-form>
        <b-form-group>
          <b-form-radio-group v-model="selectedProductType">
            <b-form-radio value="10">일반</b-form-radio>
            <b-form-radio value="20">모바일</b-form-radio>
          </b-form-radio-group>
        </b-form-group>
        <b-form-group v-if="selectedProductType === '10'">
          <b-checkbox-group v-model="checkedProduct" stacked>
            <b-checkbox v-for="(product, idx) of filteredProduct" :key="idx" :value="product">
              {{ `${product.goodsNm}${product.itemNm ? '_' + product.itemNm : ''} - ${product.prmPrc > 0 && product.prmPrc < product.salePrc ? product.prmPrc : product.salePrc}` }}
            </b-checkbox>
          </b-checkbox-group>
        </b-form-group>
        <b-form-group v-else>
          <b-form-radio-group v-model="selectedProduct" stacked>
            <b-form-radio v-for="(product, idx) of filteredProduct" :key="idx" :value="product">
              {{ `${product.goodsNm}${product.itemNm ? '_' + product.itemNm : ''} - ${product.prmPrc > 0 && product.prmPrc < product.salePrc ? product.prmPrc : product.salePrc}` }}
            </b-form-radio>
          </b-form-radio-group>
        </b-form-group>
      </b-form>
      <b-form v-if="checkedProduct && checkedProduct.length > 0">
        <b-form-group v-for="(product, idx) of checkedProduct" :key="idx" :value="product">
          {{ `${product.goodsNm}${product.itemNm ? '_' + product.itemNm : ''} - ${product.prmPrc > 0 && product.prmPrc < product.salePrc ? product.prmPrc : product.salePrc}` }}
          <b-form-input v-model="product.ordCnt" class="w-25"></b-form-input>
        </b-form-group>
      </b-form>
      <b-form v-if="selectedProduct && Object.keys(selectedProduct).length > 0">
        <b-form-group>
          {{ `${selectedProduct.goodsNm}${selectedProduct.itemNm ? '_' + selectedProduct.itemNm : ''} - ${selectedProduct.prmPrc > 0 && selectedProduct.prmPrc < selectedProduct.salePrc ? selectedProduct.prmPrc : selectedProduct.salePrc}` }}
          <b-form-input v-model="selectedProduct.ordCnt" class="w-25"></b-form-input>
        </b-form-group>
      </b-form>
      <b-form>
        <b-form-group
          label="배송비">
          {{ calculateCost }}
          <b-btn @click="getDeliveryCost">배송비조회</b-btn>
        </b-form-group>
      </b-form>
      <b-form>

        <b-btn>혜택조회</b-btn>
        <b-btn @click="calculateAmt">금액계산</b-btn>
      </b-form>
    </b-container>
  </div>
</template>

<script>
export default {
  name: "OrderPage",
  data() {
    return {
      products: [],
      costs: [],
      selectedProductType: '10',
      checkedProduct: [],
      selectedProduct: {},
      isConfirmProduct: false,
      orderForm: {
        ordNo: null,

      },
    }
  },
  async fetch() {
    this.products = await this.$axios.$get('/product/getProductList');

  },
  computed: {
    filteredProduct() {
      return this.products.filter(p => p.goodsTpCd === this.selectedProductType);
    },
    calculateCost() {
      return this.costs && this.costs.length > 0 ? this.costs?.filter(c => c.dvAmtTpCd === '10')?.map(c => c.orgDvAmt)?.reduce((pre, cur) => pre + cur) : 0;
    }
  },
  watch: {
    selectedProductType (val, oldVal) {
      this.checkedProduct.splice(0);
      this.selectedProduct = {};
    }
  },
  methods: {
    selectProduct() {
      this.isConfirmProduct = true;
    },
    async getDeliveryCost() {
      this.costs = await this.$axios.$get('/delivery/getDeliveryCost');
    },
    calculateAmt() {

    },
    setOrderForm() {
      const orderProducts = this.selectedProductType === '10' ? this.checkedProduct : Array.of(this.selectedProduct);
      this.orderForm.orderProducts = orderProducts;
    }
  },
}
</script>

<style scoped>

</style>
