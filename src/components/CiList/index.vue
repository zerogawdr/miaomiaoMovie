<template>
  <div class="cinema_body">
    <Loading v-if="isLoading"/>
    <Scroller v-else>
      <ul>
        <!-- <li>
                <div>
                    <span>大地影院(澳东世纪店)</span>
                    <span class="q"><span class="price">22.9</span> 元起</span>
                </div>
                <div class="address">
                    <span>金州区大连经济技术开发区澳东世纪3层</span>
                    <span>1763.5km</span>
                </div>
                <div class="card">
                    <div>小吃</div>
                    <div>折扣卡</div>
                </div>
        </li>-->
        <li v-for="item in cinemaList" :key="item.id">
          <div>
            <span>{{ item.nm }}</span>
            <span class="q">
              <span class="price">{{ item.sellPrice }}</span> 元起
            </span>
          </div>
          <div class="address">
            <span>{{ item.addr }}</span>
            <span>{{ item.distance }}</span>
          </div>
          <div class="card">
            <div
              v-for="(num,key) in item.tag"
              v-if="num===1"
              :key="key"
              :class=" key | classCard "
            >{{ key | formatCard }}</div>
          </div>
        </li>
      </ul>
    </Scroller>
  </div>
</template>

<script>
export default {
  name: "CiList",
  data() {
    return {
      cinemaList: [],
      isLoading: true,
      prevCityId: -1
    };
  },
  activated() {
    //因为一级路由用的kepp-alive组件嵌套的会缓存数据,需要在activated钩子函数中才能在页面渲染前获取到收据，设置了<kepp-alive>的组件muonted钩子不会触发
    var cityId = this.$store.state.city.id; //从VUEx里去除存储的city id
    if (this.prevCityId === cityId) {
      return;
    } //判断下当前页面的cityid是否和从vuex里取出来的值相等，相等则阻止下面就不从服务器取值
    this.isLoading = true; //不相等开启加载动画

    this.axios.get("/api/cinemaList?cityId=" + cityId).then(res => {
      //从远程服务器去值
      var msg = res.data.msg;
      if (msg === "ok") {
        //判断状态
        this.cinemaList = res.data.data.cinemas; //赋值
        this.isLoading = false; //吧isloading效果取消
        this.prevCityId = cityId; //赋值
      }
    });
  },
  filters: {
    //过滤器
    formatCard(key) {
      var card = [
        { key: "allowRefund", value: "改签" },
        { key: "endorse", value: "退" },
        { key: "sell", value: "折扣卡" },
        { key: "snack", value: "小吃" }
      ];
      for (var i = 0; i < card.length; i++) {
        if (card[i].key === key) {
          return card[i].value;
        }
      }
      return "";
    },
    classCard(key) {
      var card = [
        { key: "allowRefund", value: "bl" },
        { key: "endorse", value: "bl" },
        { key: "sell", value: "or" },
        { key: "snack", value: "or" }
      ];
      for (var i = 0; i < card.length; i++) {
        if (card[i].key === key) {
          return card[i].value;
        }
      }
      return "";
    }
  }
};
</script>

<style scoped>
#content .cinema_body {
  flex: 1;
  overflow: auto;
}
.cinema_body ul {
  padding: 20px;
}
.cinema_body li {
  border-bottom: 1px solid #e6e6e6;
  margin-bottom: 20px;
}
.cinema_body div {
  margin-bottom: 10px;
}
.cinema_body .q {
  font-size: 11px;
  color: #f03d37;
}
.cinema_body .price {
  font-size: 18px;
}
.cinema_body .address {
  font-size: 13px;
  color: #666;
}
.cinema_body .address span:nth-of-type(2) {
  float: right;
}
.cinema_body .card {
  display: flex;
}
.cinema_body .card div {
  padding: 0 3px;
  height: 15px;
  line-height: 15px;
  border-radius: 2px;
  color: #f90;
  border: 1px solid #f90;
  font-size: 13px;
  margin-right: 5px;
}
.cinema_body .card div.or {
  color: #f90;
  border: 1px solid #f90;
}
.cinema_body .card div.bl {
  color: #589daf;
  border: 1px solid #589daf;
}
</style>
