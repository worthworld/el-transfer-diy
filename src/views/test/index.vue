<template>
  <div class="box">
    <el-transfer-diy
      v-model="value"
      :titles="titles"
      :data="list"
      :filterable="false"
      :props="defaultProps"
      class="transfer"
    >
    </el-transfer-diy>
  </div>
</template>

<script>
import elTransferDiy from "@/components/el-transfer-diy";
export default {
  name: "TestTransfer",
  components: { elTransferDiy },
  data() {
    return {
      titles: ["列表1", "列表2"],
      list: [
        { key: 11, label: "选项一", disabled: false },
        { key: 22, label: "选项二", disabled: false },
        { key: 33, label: "选项三", disabled: false },
        { key: 4, label: "选项四", disabled: false },
        { key: 5, label: "选项五", disabled: false },
        { key: 66, label: "选项六", disabled: false },
        { key: 72, label: "选项七", disabled: false },
        { key: 8, label: "选项八", disabled: false },
        { key: 92, label: "选项九", disabled: false },
        { key: 110, label: "选项十", disabled: false },
        { key: 111, label: "选项十一", disabled: false },
        { key: 122, label: "选项十二", disabled: false }
      ],
      value: [
        { label: "突击组", children: [1] },
        { label: "掩护组", children: [2, 3] }
      ],
      defaultProps: {
        key: "key",
        label: "label",
        disabled: "disabled",
        children: "children"
      }
    };
  },
  methods: {}
};
</script>

<style lang="scss">
.box {
  width: 100%;
  height: 100vh;
  > img {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
  }
  .transfer {
    width: 80%;
    height: 50%;
    margin: 50px;
  }
}
</style>
