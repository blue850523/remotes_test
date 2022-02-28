<template>
  <div id="pagination" class="pagination">
    <div
      class="page_item"
      :class="{ disabled: page == 1 }"
      @click="page_jump(1)"
    >
      <span>{{ page_text[0] }}</span>
    </div>
    <div
      class="page_item"
      :class="{ disabled: page - 1 < 1 }"
      @click="page_jump(page - 1)"
    >
      <span>{{ page_text[1] }}</span>
    </div>
    <div
      class="page_item"
      :class="{ active_page_item: it === page }"
      v-for="it in page_items"
      :key="`page_items_${it}`"
      @click="page_jump(it)"
    >
      <span>{{ it }}</span>
    </div>
    <div
      class="page_item"
      :class="{ disabled: page + 1 > total_pages }"
      @click="page_jump(page + 1)"
    >
      <span>{{ page_text[2] }}</span>
    </div>
    <div
      class="page_item"
      :class="{ disabled: page >= total_pages }"
      @click="page_jump(total_pages)"
    >
      <span>{{ page_text[3] }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "pagination",
  data() {
    return {
      page_text: ["<<", "<", ">", ">>"],
      page: 1,
      limit: 25,
      total_records: 0,
    };
  },
  computed: {
    total_pages() {
      return Math.ceil(this.total_records / this.limit);
    },
    page_items() {
      let page = parseInt(this.page);
      let min_page = Math.max(page - 2, 1);
      let max_page = Math.min(min_page + 4, this.total_pages);
      min_page = Math.max(max_page - 4, 1);
      let page_list = [];
      for (let i = min_page; i <= max_page; i++) {
        page_list.push(i);
      }

      if (page_list.length === 0) {
        page_list.push(1);
      }

      return page_list;
    },
  },
  methods: {
    set_pagination(total_records) {
      this.page = 1;
      this.total_records = total_records;
      this.emit_page_jump(1);
    },
    page_jump(page) {
      if (!(page > 0 && page <= this.total_pages)) {
        return;
      }

      this.page = page;
      this.emit_page_jump(page);
    },
    emit_page_jump(page) {
      this.$emit("emitPageJump", page);
    },
  },
};
</script>

<style lang="scss" scoped>
.pagination {
  display: flex;
  flex-wrap: wrap;
  .page_item {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    padding: 5px;
    min-width: 25px;
    height: 25px;
    border: 2px solid #ccc;
    cursor: pointer;
  }
  .page_item:not(:first-child) {
    border-left: none;
  }
  .disabled {
    color: #ccc;
    cursor: not-allowed;
  }
  .active_page_item {
    background: #b4e7ff;
    font-weight: bold;
  }
}
</style>
