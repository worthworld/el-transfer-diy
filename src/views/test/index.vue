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
        { key: 1, label: "士兵A", disabled: false },
        { key: 2, label: "士兵B", disabled: false },
        { key: 3, label: "士兵C", disabled: false },
        { key: 4, label: "士兵D", disabled: false },
        { key: 5, label: "士兵E", disabled: false },
        { key: 6, label: "士兵F", disabled: false },
        { key: 7, label: "士兵G", disabled: false },
        { key: 8, label: "士兵H", disabled: false },
        { key: 9, label: "士兵I", disabled: false },
        { key: 10, label: "士兵J", disabled: false },
        { key: 11, label: "士兵K", disabled: false },
        { key: 12, label: "士兵L", disabled: false }
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
