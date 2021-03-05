<template>
  <div class="page">
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
        { key: 1, label: "选项一", disabled: false },
        { key: 2, label: "选项二", disabled: false },
        { key: 3, label: "选项三", disabled: false },
        { key: 4, label: "选项四", disabled: false },
        { key: 5, label: "选项五", disabled: false },
        { key: 6, label: "选项六", disabled: false },
        { key: 7, label: "选项七", disabled: false },
        { key: 8, label: "选项八", disabled: false },
        { key: 9, label: "选项九", disabled: false },
        { key: 10, label: "选项十", disabled: false },
        { key: 11, label: "选项十一", disabled: false },
        { key: 12, label: "选项十二", disabled: false }
      ],
      value: [
        { label: "突击组", children: [1] },
        { label: "掩护组", children: [2, 3] },
        { label: "狙击组", children: [4, 5] }
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
.page {
  width: 100vw;
  height: 100%;
  background-color: rgb(31, 29, 29);
  .transfer {
    width: 40%;
    height: 40%;
    margin: auto;
    padding-top: 50px;
  }
}
</style>
