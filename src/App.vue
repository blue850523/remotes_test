<template>
  <div id="app">
    <div class="container">
      <div class="header">
        <div class="search_area">
          <span class="title">國名</span>
          <el-input
            v-model="search_input"
            @keyup.enter.native="search()"
          ></el-input>
          <el-button type="primary" @click="search()">搜尋</el-button>
        </div>
        <div class="sort_area">
          <el-button type="primary" @click="set_sorting(0)">正序</el-button>
          <el-button type="primary" @click="set_sorting(1)">倒序</el-button>
        </div>
      </div>

      <div class="table_area">
        <div class="table_title">
          <el-row>
            <el-col :span="4">國旗</el-col>
            <el-col :span="4">國家名稱</el-col>
            <el-col :span="3">2位國家代碼</el-col>
            <el-col :span="3">3位國家代碼</el-col>
            <el-col :span="4">母語名稱</el-col>
            <el-col :span="3">替代國家名稱</el-col>
            <el-col :span="3">國際電話區號</el-col>
          </el-row>
        </div>
        <div class="table_responsive">
          <div class="table_body" ref="table_body">
            <el-row
              class="data_row"
              v-for="(it, index) in show_country_list"
              :key="`country_${index}`"
            >
              <el-col :span="4">
                <img :src="it.flags.png" />
              </el-col>
              <el-col :span="4">
                <a @click="more_info(it)">{{ it.name.official }}</a>
              </el-col>
              <el-col :span="3">
                {{ it.cca2 }}
              </el-col>
              <el-col :span="3">
                {{ it.cca3 }}
              </el-col>
              <el-col :span="4">
                {{ show_language(it.languages) }}
              </el-col>
              <el-col :span="3">
                {{ it.altSpellings.join(", ") }}
              </el-col>
              <el-col :span="3">
                {{ it.idd.root ? "root: " + it.idd.root : "" }}
                <br />
                {{ it.idd.suffixes ? "suffixes: " + it.idd.suffixes : "" }}
              </el-col>
            </el-row>
          </div>
        </div>
      </div>

      <div class="footer">
        <Pagination ref="pagination" @emitPageJump="page_jump" />
      </div>
    </div>

    <DeatilModal ref="deatil_modal" />
  </div>
</template>

<script>
import Pagination from "@/components/pagination";
import DeatilModal from "@/components/deatil_modal";

var API = {
  getAll: "https://restcountries.com/v3.1/all",
  getByName: "https://restcountries.com/v3.1/name",
};

export default {
  name: "App",
  components: {
    Pagination,
    DeatilModal,
  },
  data() {
    return {
      search_input: "",
      country_list: [],
      show_country_list: [],
      page: 1,
    };
  },
  created() {
    this.get_all_list();
  },
  methods: {
    async get_all_list() {
      const loading = this.$loading({
        lock: true,
        text: "Loading",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)",
      });

      await this.axios
        .get(API.getAll)
        .then((res) => {
          if (res.status === 200) {
            this.country_list = res.data;
            this.$refs.pagination.set_pagination(this.country_list.length);
          }
        })
        .catch((err) => {
          console.log(err);
          this.$message.error("取得資料失敗");
        });

      loading.close();
    },
    async search() {
      let name = this.search_input.trim();

      if (name.length > 0) {
        const loading = this.$loading({
          lock: true,
          text: "Loading",
          spinner: "el-icon-loading",
          background: "rgba(0, 0, 0, 0.7)",
        });

        await this.axios
          .get(API.getByName + `/${name}`)
          .then((res) => {
            if (res.status === 200) {
              this.country_list = res.data;
              this.$refs.pagination.set_pagination(this.country_list.length);
            }
          })
          .catch((err) => {
            console.log(err);
            this.$message.error("查無相關資料");
            this.country_list = [];
            this.show_country_list = [];
            this.$refs.pagination.set_pagination(0);
          });

        loading.close();
      } else {
        this.get_all_list();
      }
    },
    set_sorting(direction) {
      if (direction === 0) {
        this.country_list.sort(function (a, b) {
          if (a.name.official < b.name.official) return -1;
          if (a.name.official > b.name.official) return 1;
          return 0;
        });
      } else {
        this.country_list.sort(function (a, b) {
          if (a.name.official < b.name.official) return 1;
          if (a.name.official > b.name.official) return -1;
          return 0;
        });
      }

      this.page_jump(this.page);
    },
    more_info(data) {
      this.$refs.deatil_modal.open_modal(data);
    },
    show_language(languages) {
      let arr = [];
      if (languages) {
        Object.keys(languages).forEach((it) => {
          arr.push(`${it}: ${languages[it]}`);
        });
      }
      return arr.join(", ");
    },
    scroll_to_top() {
      this.$refs.table_body.scrollTop = 0;
    },
    page_jump(page) {
      this.page = page;
      let slice_data_list = [];
      for (let i = 0; i < this.country_list.length; i += 25) {
        slice_data_list.push(this.country_list.slice(i, i + 25));
      }
      this.show_country_list = slice_data_list[page - 1];
      this.scroll_to_top();
    },
  },
};
</script>

<style lang="scss" scoped>
#app {
  .container {
    display: flex;
    flex-direction: column;
    box-sizing: border-box;
    padding: 10px;
    height: 100vh;
    .title {
      font-weight: bold;
    }
    .header {
      display: flex;
      justify-content: space-between;
      flex-wrap: wrap;
      .search_area {
        display: flex;
        align-items: center;
        margin-bottom: 10px;
        .el-input {
          width: 200px;
          margin: 0 10px;
        }
      }
      .sort_area {
        margin-bottom: 10px;
      }
    }
    .table_area {
      flex: 1;
      display: flex;
      flex-direction: column;
      min-width: 900px;
      .el-col {
        padding: 5px 10px;
        text-align: center;
        word-wrap: break-word;
        a {
          color: #1cb8ff;
          text-decoration: underline;
          cursor: pointer;
        }
      }
      .table_title {
        padding-right: 18px;
        font-weight: bold;
        background-color: #ecf5ff;
      }
      .table_responsive {
        flex: 1;
        display: flex;
        flex-direction: column;
        position: relative;
        .table_body {
          display: flex;
          flex-direction: column;
          position: absolute;
          top: 0;
          left: 0;
          bottom: 0;
          right: 0;
          overflow-y: scroll;
          img {
            width: 100%;
          }
          .data_row {
            border-bottom: 2px solid #eeeeee;
          }
        }
      }
    }
    .footer {
      padding: 5px 0;
    }
  }
}
</style>
