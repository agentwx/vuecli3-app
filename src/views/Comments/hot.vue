<template>
  <div id="hot">
    <section class="comments-content">
      <short-comments :ShortCommit="commitsHotObj" :isShow="false"/>
      <van-pagination
        v-model="currentPage"
        :page-count="Math.ceil(commitsHotObj.total / 25)"
        mode="multi"
        force-ellipses
        @change="getPageHotVal"
      />
      <section id="loading">
        <van-loading color="green" v-show="isLoading"/>
      </section>
    </section>
  </div>
</template>

<script>
import shortComments from "@/components/shortComments";

export default {
  name: "hot",
  data() {
    return {
      isLoading: true,
      commitsHotObj: null,
      currentPage: 1
    };
  },
  components: {
    shortComments
  },
  methods: {
    paramObj($type) {
      let _this = this;
      let params = {
        count: 25,
        order_by: $type,
        start: (_this.currentPage - 1) * 25,
        ck: "",
        for_mobile: 1
      };
      return params;
    },
    hotParams() {
      return this.$server.shotCommits(
        this.$route.query.type,
        this.$route.query.id,
        this.paramObj("hot")
      );
    },
    getPageHotVal(value) {
      this.currentPage = value;
      this.hotParams().then(res => {
        this.isLoading = false;
        this.commitsHotObj = res;
      });
    }
  },
  mounted() {
    this.hotParams().then(res => {
      this.isLoading = false;
      this.commitsHotObj = res;
    });
  }
};
</script>

<style scoped lang="scss">
@import "../../assets/scss/mixin.scss";

.comments {
  &-subjects {
    @include flex;
    @include fl_column_center;
    @include sizeColor(16px, #42bd56, inherit);
    padding: 12px 0;
    border-bottom: 1px solid #f3f3f3;
    img {
      @include wh(17px, 24px);
      margin-right: 6px;
    }
    span:last-child {
      margin-left: 2px;
      color: #b4b4b4;
      white-space: nowrap;
    }
  }
  &-title {
    margin-bottom: 30px;
    h1 {
      @include sizeColor(24px, #000, inherit);
      margin: 30px 70px 5px 0;
      font-weight: normal;
      line-height: 32px;
      word-break: break-all;
    }
  }
  &-content {
    position: relative;
    margin-top: 16px;
  }
}
#loading {
  position: absolute;
  top: 100px;
  left: 50%;
  transform: translateX(-50%);
}
</style>