<template>
  <div class="deatil_modal">
    <el-dialog
      title="國家資訊"
      width="80%"
      top="5vh"
      :visible.sync="show_modal"
      :close-on-click-modal="false"
      :close-on-press-escape="false"
      :show-close="false"
    >
      <div class="content">
        <div v-for="it in Object.keys(data)" :key="it">
          <div class="item_group" v-if="show_obj[it]">
            <template v-if="it === 'coatOfArms' || it === 'flags'">
              <div class="title">{{ get_title(it) }}</div>
              <div class="text"><img :src="data[it].png" /></div>
            </template>
            <template v-else>
              <div class="title">{{ get_title(it) }}</div>
              <div class="text" v-html="get_text(it)"></div>
            </template>
          </div>
        </div>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="close_modal()">關閉</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "deatil_modal",
  data() {
    return {
      show_modal: false,
      data: {},
      show_obj: {
        altSpellings: "替代名稱",
        area: "面積",
        borders: "鄰近國家",
        capital: "首都",
        capitalInfo: "首都位置",
        car: "汽車規則",
        cca2: "2位國家代碼",
        cca3: "3位國家代碼",
        ccn3: "國家數字代碼",
        cioc: "cioc",
        coatOfArms: "國徽",
        continents: "大陸",
        currencies: "幣種",
        fifa: "FIFA",
        flag: "旗幟",
        flags: "國旗",
        idd: "國際電話區號",
        independent: "獨立",
        landlocked: "內陸",
        languages: "語言",
        latlng: "經緯",
        maps: "地圖",
        name: "名稱",
        population: "人口",
        postalCode: "郵政編號",
        region: "地區",
        startOfWeek: "周開始",
        status: "狀態",
        subregion: "次區域",
        timezones: "時區",
        tld: "頂級域名",
        translations: "翻譯",
        unMember: "聯合國會員",
      },
    };
  },
  methods: {
    open_modal(data) {
      this.show_modal = true;
      this.data = data;
    },
    close_modal() {
      this.show_modal = false;
      this.data = {};
    },
    get_title(key) {
      return this.show_obj[key];
    },
    get_text(key) {
      if (
        typeof this.data[key] === "object" &&
        Object.keys(this.data[key]).length === 0
      ) {
        return "";
      }

      if (
        [
          "altSpellings",
          "borders",
          "capital",
          "continents",
          "timezones",
          "tld",
        ].indexOf(key) !== -1
      ) {
        return this.data[key].join(", ");
      } else if (
        [
          "area",
          "cca2",
          "cca3",
          "ccn3",
          "cioc",
          "fifa",
          "flag",
          "population",
          "region",
          "startOfWeek",
          "status",
          "subregion",
        ].indexOf(key) !== -1
      ) {
        return this.data[key];
      } else if (["capitalInfo"].indexOf(key) !== -1) {
        return `(${this.data[key].latlng.join(", ")})`;
      } else if (
        ["car", "idd", "languages", "maps", "postalCode"].indexOf(key) !== -1
      ) {
        let arr = [];
        Object.keys(this.data[key]).forEach((it) => {
          arr.push(`${it}: ${this.data[key][it]}`);
        });
        return arr.join(", <br/>");
      } else if (["currencies", "translations"].indexOf(key) !== -1) {
        let arr = [];
        Object.values(this.data[key]).forEach((it, index) => {
          let arr2 = [];
          Object.keys(it).forEach((it2) => {
            arr2.push(`${it2}: ${it[it2]}`);
          });
          arr.push(`${Object.keys(this.data[key])[index]}(${arr2.join(", ")})`);
        });
        return arr.join(", <br/>");
      } else if (
        ["independent", "landlocked", "unMember"].indexOf(key) !== -1
      ) {
        return this.data[key] ? "是" : "否";
      } else if (["latlng"].indexOf(key) !== -1) {
        return `(${this.data[key].join(", ")})`;
      } else if (["name"].indexOf(key) !== -1) {
        let arr = [];
        Object.keys(this.data[key]).forEach((it) => {
          if (it === "nativeName") {
            let arr2 = [];
            Object.values(this.data[key][it]).forEach((it2) => {
              arr2.push(`${it2.official}(${it2.common})`);
            });
            arr.push(`${it}: ${arr2.join(", ")}`);
          } else {
            arr.push(`${it}: ${this.data[key][it]}`);
          }
        });
        return arr.join(",  <br/>");
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.content {
  font-size: 16px;
  .item_group {
    display: flex;
    border-bottom: 2px solid #ccc;
    .title,
    .text {
      display: flex;
      align-items: center;
      padding-left: 10px;
      img {
        height: 120px;
      }
    }
    .title {
      min-width: 200px;
      min-height: 40px;
      font-weight: bold;
      background-color: #ececec;
    }
  }
}
</style>
