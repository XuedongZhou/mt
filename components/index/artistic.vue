<template>
  <section class="m-istyle">
    <dl @mouseover="over">
      <dt>有格调</dt>
      <dd
        :class="{active:kind==='all'}"
        keyword="景点"
        kind="all"
      >全部</dd>
      <dd
        :class="{active:kind==='part'}"
        keyword="美食"
        kind="part"
      >约会聚餐</dd>
      <dd
        :class="{active:kind==='spa'}"
        keyword="丽人"
        kind="spa"
      >丽人SPA</dd>
      <dd
        :class="{active:kind==='movie'}"
        keyword="电影"
        kind="movie"
      >电影演出</dd>
      <dd
        :class="{active:kind==='travel'}"
        keyword="旅游"
        kind="travel"
      >品质出游</dd>
    </dl>
    <ul class="ibody">
      <li
        :key="item.title"
        v-for="item in cur"
      >
        <el-card
          :body-style="{padding: '0px'}"
          shadow="never"
        >
          <img
            :alt="item.title"
            :src="item.img"
            class="image"
          />
          <ul class="cbody">
            <li class="title">{{ item.title }}</li>
            <li class="pos">
              <span>{{ item.pos }}</span>
            </li>
            <li class="price">
              ￥
              <em>{{ item.price }}</em>
              <span>/起</span>
            </li>
          </ul>
        </el-card>
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  data() {
    return {
      kind: "all",
      list: {
        all: [],
        part: [],
        spa: [],
        movie: [],
        travel: []
      }
    };
  },
  computed: {
    cur() {
      return this.list[this.kind];
    }
  },
  methods: {
    async over(e) {
      let dom = e.target;
      let tag = dom.tagName.toLowerCase();
      if (tag === "dd") {
        this.kind = dom.getAttribute("kind");
        let keyword = dom.getAttribute("keyword");
        let {
          status,
          data: { count, pois }
        } = await this.$axios.get("/search/resultsByKeywords", {
          params: {
            keyword,
            city: this.$store.state.geo.position.city
          }
        });
        if (status === 200 && count > 0) {
          let r = pois
            .filter(item => item.photos.length)
            .map(val => {
              return {
                title: val.name,
                pos: val.type.split(";")[0],
                price: val.biz_ext.cost || "暂无",
                img: val.photos[0].url,
                url: "//abc.com"
              };
            });
          this.list[this.kind] = r.slice(0, 9);
        } else {
          this.list[this.kind] = [];
        }
      }
    }
  },
  async mounted() {
    let {
      status,
      data: { count, pois }
    } = await this.$axios.get("/search/resultsByKeywords", {
      params: {
        keyword: "景点",
        city: this.$store.state.geo.position.city
      }
    });
    if (status === 200 && count > 0) {
      let r = pois
        .filter(item => item.photos.length)
        .map(val => {
          return {
            title: val.name,
            pos: val.type.split(";")[0],
            price: val.biz_ext.cost || "暂无",
            img: val.photos[0].url,
            url: "//abc.com"
          };
        });
      this.list[this.kind] = r.slice(0, 9);
    } else {
      this.list[this.kind] = [];
    }
  }
};
</script>

<style lang='scss'>
@import "@/assets/css/index/artistic.scss";
</style>
