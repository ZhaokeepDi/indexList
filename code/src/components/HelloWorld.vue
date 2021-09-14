<template>
  <div class="content">
    <div class="relative">
      <div class="main" @scroll="scrollFn" id="main">
        <div id="scrollView">
          <header>页面头部，暂时用颜色快代替</header>
          <main>
            <div class="goods">
              <div class="menu-box" id="menuBox">
                <Menu
                  :data="menu"
                  :action="action"
                  :class="{ istop: istop }"
                  @clickEvent="menuClick"
                />
              </div>

              <div class="list">
                <div v-for="item of list" :key="item.key" :id="item.key">
                  {{ item.text }}
                </div>
              </div>
            </div>
          </main>
        </div>
      </div>
    </div>
    <footer>页面底部</footer>
  </div>
</template>

<script>
import { reactive, ref, onMounted, getCurrentInstance } from "vue";
import Menu from "./menu.vue";
export default {
  components: {
    Menu,
  },
  methods: {
    fd(callback, delay = 10) {
      let timeout;
      return () => {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
          callback();
        }, delay);
      };
    },
  },
  setup() {
    // 数据初始化
    //商品类型选项
    let menu = reactive([]);
    for (let i = 0; i < 9; i++) {
      menu.push({
        text: "菜单" + i,
        key: i + "",
        children: [
          { text: "菜单" + i + "-1", key: i + "-1" },
          { text: "菜单" + i + "-2", key: i + "-2" },
          { text: "菜单" + i + "-3", key: i + "-3" },
        ],
      });
    }

    // 商品列表选项
    let list = reactive([]);
    for (let i = 0; i < 9; i++) {
      // for (let j = 0; j < Math.ceil(Math.random() * 15); j++) {
      list.push({ text: "商品选项，类型：" + i, key: i + "" });
      list.push({ text: "商品" + i + "-1", key: i + "-1" });
      list.push({ text: "商品" + i + "-2", key: i + "-2" });
      list.push({ text: "商品" + i + "-3", key: i + "-3" });
      // }
    }

    // 商品类型默认不置顶
    let istop = ref(false);
    let action = ref("0");

    // 需要用来计算的数据获取
    let scrollView;
    let scrollHeight;
    onMounted(() => {
      scrollView = document.querySelector("#main");
      // 总内容高度-框子高度=滚动条总高度（算得1572）
      scrollHeight =
        document.querySelector("#scrollView").clientHeight -
        scrollView.clientHeight;
    });

    // 滚动监听
    const scrollFn = getCurrentInstance().type.methods.fd(() => {
      let scrollTop = scrollView.scrollTop;
      // 置顶判断
      if (scrollTop > 300) {
        istop.value = true;
      } else {
        istop.value = false;
      }

      //触底判断,这里有前面的顶死高度改为百分比计算
      //滚动高度/总滚动条高度，获取当前滚动比值
      let valuation = (scrollTop / scrollHeight).toFixed(5);
      //拿到对应比值范围的list中某一项
      action.value =
        list[parseInt(valuation * (list.length - 1))]?.key || action.value;
    });

    // 监听菜单点击跳转
    let scrollBox;
    function menuClick(key) {
      if (action.value === key) return;
      // document.location.href=document.location.origin+'/#goods'+key
      scrollBox = scrollBox || document.querySelector("#main");
      // 查找跳转位置
      const start = list.findIndex((item) => {
        return item.key === key;
      });
      // 计算跳跃距离，这里也改用百分比计算,start加0.5是因为计算必须要比自身大才能把自身显示出来，
      let num =
        (start === 0 ? 0 : ((start + 0.5) * scrollHeight) / (list.length - 1)) -
        scrollBox.scrollTop;
      // 动画
      let step = num / 20;
      for (let i = 0; i < 20; i++) {
        setTimeout(() => {
          scrollBox.scrollTop += step;
        }, i * 10);
      }
    }
    return { menu, list, scrollFn, istop, action, menuClick };
  },
};
</script>

<style scoped>
.istop {
  position: absolute;
  top: 0;
  left: 0;
}
.content {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.main {
  height: 100%;
  overflow: auto;
}
.relative {
  flex: 1;
  position: relative;
  overflow: hidden;
}
header {
  height: 300px;
  background-color: darkcyan;
  color: white;
  font-size: 1.4em;
  text-align: center;
  line-height: 300px;
}
footer {
  height: 100px;
  background-color: darkkhaki;
  color: white;
  font-size: 1.4em;
  text-align: center;
  line-height: 100px;
}
main {
  flex: 1;
}
.goods {
  display: flex;
  flex-direction: row;
  height: 100%;
}

.menu-box {
  width: 80px;
}
.list > div {
  line-height: 50px;
  font-size: 1.4em;
}
</style>
