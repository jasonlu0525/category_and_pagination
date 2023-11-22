<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination justify-content-center">
      <li class="page-item" :class="pages.has_pre ? '' : 'disabled'">
        <a class="page-link" href="#" aria-label="Previous">
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <li>
        <a
          class="page-link"
          href="#"
          :class="{ active: pages.current_page === 1 }"
          @click.prevent="updatePage(1)"
          >{{ 1 }}</a
        >
      </li>
      <li v-if="pages.current_page > 2">...</li>
      <li
        class="page-item"
        v-for="page in pages.total_pages"
        :key="page"
        :class="{ active: page === pages.current_page }"
      >
        <a
          class="page-link"
          href="#"
          v-if="
            page !== 1 &&
            page !== pages.total_pages &&
            page <= pages.total_pages &&
            page < pages.current_page + 2 &&
            page > pages.current_page - 2
          "
          @click.prevent="updatePage(page)"
          >{{ page }}</a
        >
      </li>
      <li v-if="pages.current_page < pages.total_pages - 2">...</li>
      <li>
        <a
          class="page-link"
          href="#"
          :class="{ active: pages.current_page === pages.total_pages }"
          @click.prevent="updatePage(pages.total_pages)"
          >{{ pages.total_pages }}</a
        >
      </li>
      <li class="page-item" :class="pages.has_next ? '' : 'disabled'">
        <a class="page-link" href="#" aria-label="Next">
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>
<script>
export default {
  props: ['pages'],
  methods: {
    updatePage(page) {
      this.$emit('emit-pages', page);
    },
  },
};
</script>
