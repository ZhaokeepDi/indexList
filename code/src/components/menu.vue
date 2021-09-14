<script>
export default {
  props: {
    data: {
      type: Array,
      require: true,
    },
    action: {
      type: String,
      require: true,
    },
  },
  render() {
    return <div class="menu"> {this.getGroup()}</div>;
  },
  methods: {
    getGroup() {
      let menuGroup = [];
      for (let item of this.data) {
        let content;
        let child = [];
        if (item.children) {
          // 自身颜色判断
          child.push(
            this.getItem(item.text, item.key, {
              style: this.action === item.key ? "color:red" : "",
            })
          );
          for (let c of item.children) {
            //子颜色判断
            child.push(
              this.getItem(c.text, c.key, {
                style: this.action === c.key ? "color:red" : "",
              })
            );
          }
          content = this.getItem(child, item.key, {
            class:
              "group" +
              (this.action.split("-")[0] === item.key.split("-")[0]
                ? " open"
                : ""),
          });
        } else {
          content = this.getItem(item.text, item.key);
        }
        menuGroup.push(content);
      }
      return menuGroup;
    },
    getItem(content, key, props = []) {
      return (
        <div
          key={key}
          {...props}
          onClick={() => {
            this.clickE(key);
          }}
        >
          {content}
        </div>
      );
    },
    clickE(key) {
      window.event.stopPropagation();
      this.$emit("clickEvent", key);
    },
  },
};
</script>
<style>
.menu {
  /* overflow: auto; */
  font-size: 1.4em;
}
.group {
  max-height: 50px;
  overflow: hidden;
}
.open {
  transition: all 0.8s;
  max-height: 400px !important;
  overflow: unset;
  background: #333;
  color: white;
}
.menu div {
  line-height: 50px;
  width: 80px;
  text-align: center;
}
</style>
