<template>
  <!-- <li class="sku-row-item"> -->
  <a href="javascript:;"
     class="sku-row-item van-button van-button--default van-button--small van-hairline--surround"
     :class="{
        'sku-row-item-active': isChoosed,
        'sku-row-item-disabled': !isChoosable,
      }"
     @click="onSelect">
    <span> {{ skuTreeValue.name }} </span>
  </a>
  <!-- </li> -->
</template>

<script>
import { isSkuChoosable } from "./utils";
export default {
  props: {
    skuEventBus: Object,
    skuTreeValue: Object,
    skuList: Array,
    selectedSku: Object,
    skuKeyStr: String,
    initSelected: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    isChoosed() {
      return this.skuTreeValue.id
        ? this.skuTreeValue.id === this.selectedSku[this.skuKeyStr]
        : false;
    },
    isChoosable() {
      const isChoosable = isSkuChoosable(this.skuList, this.selectedSku, {
        key: this.skuKeyStr,
        valueId: this.skuTreeValue.id
      });
      return isChoosable;
    }
  },
  watch: {
    skuTreeValue(val) {
      // 监听skuItem数据
      // 如果有初始化选中，则默认选中该规格
      if (this.initSelected) {
        this.onSelect();
      }
    }
  },
  methods: {
    onSelect() {
      if (this.isChoosable) {
        this.skuEventBus.$emit("select", {
          ...this.skuTreeValue,
          skuKeyStr: this.skuKeyStr
        });
      }
    }
  },
  mounted() {
    // 如果有初始化选中，则默认选中该规格
    if (this.initSelected) {
      this.onSelect();
    }
  }
};
</script>
<style lang="stylus" scoped>
.sku-row-item {
  color: #000
}
</style>
