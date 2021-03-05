<template>
  <div class="el-transfer">
    <left-panel
      ref="leftPanel"
      v-bind="$props"
      :data="sourceData"
      :title="titles[0] || t('el.transfer.titles.0')"
      :default-checked="leftDefaultChecked"
      :placeholder="filterPlaceholder || t('el.transfer.filterPlaceholder')"
      @checked-change="onSourceCheckedChange"
    >
      <slot name="left-footer"></slot>
    </left-panel>
    <div class="el-transfer__buttons">
      <div class="el-transfer__buttons_wrap">
        <el-button
          type="primary"
          :class="[
            'el-transfer__button',
            hasButtonTexts ? 'is-with-texts' : ''
          ]"
          :disabled="rightChecked.length === 0"
          @click.native="addToLeft"
        >
          <i class="el-icon-arrow-left"></i>
          <span v-if="buttonTexts[0] !== undefined">{{ buttonTexts[0] }}</span>
        </el-button>
        <el-button
          type="primary"
          :class="[
            'el-transfer__button',
            hasButtonTexts ? 'is-with-texts' : ''
          ]"
          :disabled="leftChecked.length === 0"
          @click.native="addToRight"
        >
          <span v-if="buttonTexts[1] !== undefined">{{ buttonTexts[1] }}</span>
          <i class="el-icon-arrow-right"></i>
        </el-button>
      </div>
    </div>
    <right-panel
      ref="rightPanel"
      v-bind="$props"
      :data="targetData"
      :title="titles[1] || t('el.transfer.titles.1')"
      :default-checked="rightDefaultChecked"
      :placeholder="filterPlaceholder || t('el.transfer.filterPlaceholder')"
      @checked-change="onTargetCheckedChange"
    ></right-panel>

    <slot name="right-footer"></slot>
  </div>
</template>

<script>
import Emitter from "./mixins/emitter";
import Locale from "./mixins/locale";
import LeftPanel from "./left-panel.vue";
import RightPanel from "./right-panel";
import Migrating from "./mixins/migrating";

export default {
  name: "TreeElTransfer",
  components: {
    LeftPanel,
    RightPanel
  },

  mixins: [Emitter, Locale, Migrating],

  props: {
    data: {
      type: Array,
      default() {
        return [];
      }
    },
    titles: {
      type: Array,
      default() {
        return [];
      }
    },
    buttonTexts: {
      type: Array,
      default() {
        return [];
      }
    },
    filterPlaceholder: {
      type: String,
      default: ""
    },
    filterMethod: Function,
    leftDefaultChecked: {
      type: Array,
      default() {
        return [];
      }
    },
    rightDefaultChecked: {
      type: Array,
      default() {
        return [];
      }
    },
    renderContent: Function,
    // value格式： [ {  label:"组A" ,children:[1]  } ]
    value: {
      type: Array,
      default() {
        return [];
      }
    },
    format: {
      type: Object,
      default() {
        return {};
      }
    },
    filterable: Boolean,
    props: {
      type: Object,
      default() {
        return {
          label: "label",
          key: "key",
          disabled: "disabled",
          children: "children"
        };
      }
    },
    targetOrder: {
      type: String,
      default: "original"
    }
  },

  data() {
    return {
      leftChecked: [],
      rightChecked: [],
      currentGroup: 0
    };
  },

  computed: {
    dataObj() {
      const key = this.props.key;
      return this.data.reduce((o, cur) => (o[cur[key]] = cur) && o, {});
    },

    sourceData() {
      let checkedValue = [];
      this.value.map((item) => {
        if (item[this.props.children] && item[this.props.children].length > 0) {
          checkedValue = checkedValue.concat(item[this.props.children]);
        }
      });
      return this.data.filter(
        (item) => checkedValue.indexOf(item[this.props.key]) === -1
      );
    },

    targetData() {
      let arr = [];
      this.value.map((item, index) => {
        arr[index] = {
          [this.props.label]: item[this.props.label],
          [this.props.children]: this.data.filter(
            (sub) => item[this.props.children].indexOf(sub[this.props.key]) > -1
          )
        };
      });
      return arr;
      //  if (this.targetOrder === 'original') {
      //     return this.data.filter(item => this.value.indexOf(item[this.props.key]) > -1);
      //   } else {
      //     return this.value.reduce((arr, cur) => {
      //       const val = this.dataObj[cur];
      //       if (val) {
      //         arr.push(val);
      //       }
      //       return arr;
      //     }, []);
      //   }
    },

    hasButtonTexts() {
      return this.buttonTexts.length === 2;
    }
  },

  watch: {
    value(val) {
      this.dispatch("ElFormItem", "el.form.change", val);
    }
  },

  methods: {
    getMigratingConfig() {
      return {
        props: {
          "footer-format": "footer-format is renamed to format."
        }
      };
    },

    onSourceCheckedChange(val, movedKeys) {
      this.leftChecked = val;
      if (movedKeys === undefined) return;
      this.$emit("left-check-change", val, movedKeys);
    },

    onTargetCheckedChange(val, currentGroup, movedKeys) {
      this.rightChecked = val;
      this.currentGroup = currentGroup;
      if (movedKeys === undefined) return;
      this.$emit("right-check-change", val, movedKeys);
    },

    addToLeft() {
      let currentValue = this.value.slice();
      let list = currentValue[this.currentGroup][this.props.children];
      this.rightChecked.forEach((item) => {
        const index = list.indexOf(item);
        if (index > -1) {
          list.splice(index, 1);
        }
      });
      this.$emit("input", currentValue);
      this.$emit("change", currentValue, "left", this.rightChecked);
    },

    addToRight() {
      if (typeof this.currentGroup != "number") return;
      let currentValue = this.value.slice();
      let currentChildren =
        currentValue[this.currentGroup][this.props.children];
      const itemsToBeMoved = [];
      const key = this.props.key;
      let list = this.value[this.currentGroup][this.props.children];
      this.data.forEach((item) => {
        const itemKey = item[key];
        if (
          this.leftChecked.indexOf(itemKey) > -1 &&
          list.indexOf(itemKey) === -1
        ) {
          itemsToBeMoved.push(itemKey);
        }
      });
      currentValue[this.currentGroup][this.props.children] =
        this.targetOrder === "unshift"
          ? itemsToBeMoved.concat(currentChildren)
          : currentChildren.concat(itemsToBeMoved);
      this.$emit("input", currentValue);
      this.$emit("change", currentValue, "right", this.leftChecked);
    },
    // addToLast() {
    //   let currentValue = this.value.slice();
    //   const itemsToBeMoved = [];
    //   const key = this.props.key;
    //   this.data.forEach((item) => {
    //     const itemKey = item[key];
    //     if (
    //       this.leftChecked.indexOf(itemKey) > -1 &&
    //       this.value.indexOf(itemKey) === -1
    //     ) {
    //       itemsToBeMoved.push(itemKey);
    //     }
    //   });
    //   currentValue =
    //     this.targetOrder === "unshift"
    //       ? itemsToBeMoved.concat(currentValue)
    //       : currentValue.concat(itemsToBeMoved);
    //   this.$emit("input", currentValue);
    //   this.$emit("change", currentValue, "right", this.leftChecked);
    // },
    clearQuery(which) {
      if (which === "left") {
        this.$refs.leftPanel.query = "";
      } else if (which === "right") {
        this.$refs.rightPanel.query = "";
      }
    }
  }
};
</script>
<style lang="scss" scoped>
::v-deep {
  .el-transfer-panel {
    height: 100%;
    width: 40%;
  }
  .el-transfer-panel__body {
    height: 100%;
  }
  .el-transfer-panel__list {
    height: 85%;
  }
  .el-button + .el-button {
    margin-left: 0;
  }
}
.el-transfer {
  height: 100%;
}
.el-transfer__buttons {
  height: 40%;
  padding: 0 15px;
}
.el-transfer__buttons_wrap {
  display: flex;
  height: 100%;
  flex-direction: column;
  justify-content: space-around;
}
.el-transfer__button {
  border-radius: 50%;
  width: 46px;
  height: 46px;
}
</style>
