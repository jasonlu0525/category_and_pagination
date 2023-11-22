<template>
  <Loading :active="isLoading"></Loading>
  <section class="container py-5" id="product">
    <h2 class="text-center mb-4">精選課程</h2>
    <!-- <ul class="nav nav-pills justify-content-center mb-4">
      <li class="nav-item mx-2">
        <a class="nav-link active" aria-current="page" href="#">全部</a>
      </li>
      <li class="nav-item mx-2">
        <a class="nav-link" href="#">初階</a>
      </li>
      <li class="nav-item mx-2">
        <a class="nav-link" href="#">中階</a>
      </li>
      <li class="nav-item mx-2">
        <a class="nav-link" href="#">進階</a>
      </li>

    </ul> -->
    <div
      class="btn-group d-flex justify-content-center"
      role="group"
      aria-label="Basic radio toggle button group"
    >
      <input
        type="radio"
        class="btn-check"
        name="btnradio"
        id="btnradio1"
        autocomplete="off"
        v-model="category"
        value="全部"
      />
      <label class="btn btn-outline-primary" for="btnradio1" value="0">全部</label>

      <input
        type="radio"
        class="btn-check"
        name="btnradio"
        id="btnradio2"
        autocomplete="off"
        v-model="category"
        value="初階"
      />
      <label class="btn btn-outline-primary" for="btnradio2" value="1">初階</label>

      <input
        type="radio"
        class="btn-check"
        name="btnradio"
        id="btnradio3"
        autocomplete="off"
        v-model="category"
        value="中階"
      />
      <label class="btn btn-outline-primary" for="btnradio3" value="2">中階</label>
      <input
        type="radio"
        class="btn-check"
        name="btnradio"
        id="btnradio4"
        autocomplete="off"
        v-model="category"
        value="進階"
      />
      <label class="btn btn-outline-primary" for="btnradio4" value="3">進階</label>
    </div>
    {{ category }}
    <div class="row g-4 pb-5">
      <price-card
        v-for="(product, index) in productList"
        :key="index"
        :id="product.id"
        :title="product.title"
        :content="product.content"
        :image="product.imageUrl"
        :image-alt="product.title"
        :price="product.price"
        :origin_price="product.origin_price"
      />
    </div>
    {{ paginationRules }}
    <pagination :pages="paginationRules" @emit-pages="getProducts"></pagination>
  </section>
</template>
<script>
import PriceCard from '@/components/PriceCard.vue';
import Pagination from '@/components/Pagination.vue';
import productStore from '@/stores/productStore.js';
import statusStore from '@/stores/statusStore.js';
import { mapState, mapActions } from 'pinia';
import gsap from 'gsap';
export default {
  components: {
    'price-card': PriceCard,
    pagination: Pagination,
  },
  data() {
    return {
      category: '全部',
      productList: [], // 預設顯示全部類別的資料，當 category 有變動就調整資料。
      paginationRules: {}, // 換頁邏輯，如果是全部類別，則帶入 API 的 pagination ，若不是，則自己寫換頁邏輯
    };
  },
  computed: {
    ...mapState(statusStore, ['isLoading']),
    ...mapState(productStore, ['sortProducts', 'pagination']),
  },
  watch: {
    // 元件初始化，把 productStore.js 的 sortProducts 和 pagination 狀態抓出來
    sortProducts() {
      this.productList = this.sortProducts;
      this.paginationRules = this.pagination;
    },
    category(type) {
      // category 變動 ( 可能是 全部切換到其他種類或是 其他種類切換到全部) => 篩選 sortProducts 的資料

      const selectedProducts = this.sortProducts.filter((item) =>
        type !== '全部' ? item.category == type : item
      );

      // 計算總頁數 ( 一頁 10 筆 )
      const total_pages = Math.ceil(selectedProducts.length / 10);

      let selectedCategoryCollection = [];
      const defaultCurrentPage = 1;

      if (type === '全部') {
        // 切換到全部，不做分頁切割的處理
        this.productList = selectedProducts;
      } else {
        console.log(selectedProducts);


        for (let pageNum = 1; pageNum <= total_pages; pageNum++) {
          // 每 10 筆資料切割成一個陣列 ( 要注意 slice 是 shallow copy )
          // 0~ 9
          // 10 ~ 19
          // 20 ~ 29
          selectedCategoryCollection.push(
            selectedProducts.slice(10 * (pageNum - 1), 10 * pageNum)
          );
        }

        this.productList = selectedCategoryCollection[defaultCurrentPage - 1];
      }
      // ------------- 處理頁碼 ------------------
      // 當 type 是  '全部'，用 store 的邏輯，否則自己計算換頁規則
      this.paginationRules =
        type === '全部'
          ? this.pagination
          : {
              rules: '自定ˋ',
              total_pages ,
              current_page: defaultCurrentPage,
              has_pre: defaultCurrentPage - 1 > 0,
              has_next: defaultCurrentPage < total_pages,
            };
    },
  },
  methods: {
    ...mapActions(productStore, ['getAllProducts', 'getProducts']),
  },
  created() {
    // this.getAllProducts()
    this.getProducts();
  },
  mounted() {
    gsap.set('#product', { opacity: 0 });
    gsap.to('#product', {
      opacity: 1,
      delay: 0.5,
    });
  },
};
</script>
