<template>
  <div class='sku'>
    <sku-row v-for="(skuTreeItem, index) in skuTree"
             :key="index"
             :sku-row="skuTreeItem"
             :isShowTitle="!hideTitle">
      <sku-select-row-item v-for="(skuTreeValue, index) in skuTreeItem.v"
                           :key="index"
                           :initSelected="skuTreeItem.v.length === 1"
                           :sku-key-str="skuTreeItem.k_s"
                           :sku-tree-value="skuTreeValue"
                           :sku-event-bus="skuEventBus"
                           :selected-sku="selectedSku"
                           :sku-list="sku.list" />
    </sku-row>
  </div>
</template>

<script>
import Vue from "vue";
import SkuRow from "./SkuSelectRow";
import SkuSelectRowItem from "./SkuSelectRowItem";
import { getSkuResultGroup, getMatchedSku } from "./utils";
export default {
  components: {
    SkuRow,
    SkuSelectRowItem
  },
  props: {
    sku: Object,
    hideTitle: {
      type: Boolean,
      default: false
    },
    initialSku: {
      type: Object,
      default: () => ({})
    }
  },
  data() {
    return {
      selectedSku: {},
      selectedValue: {},
      isSelectedSkuInited: false,
      skuEventBus: new Vue(),
      matchedSku: {
        totalStock: 0
      },
      quantity: 1
    };
  },
  computed: {
    skuTree() {
      return this.sku.tree || [];
    },
    totalStock() {
      return this.matchedSku && this.matchedSku.totalStock
        ? this.matchedSku.totalStock
        : 0;
    }
  },
  created() {
    this.skuEventBus.$on("select", this.onSelect);
    this.resetSelectedSku(this.skuTree);
  },
  methods: {
    resetSelectedSku(skuTree) {
      this.selectedSku = {};
      // 重置selectedSku
      skuTree.forEach(item => {
        this.selectedSku[item.k_s] = this.initialSku[item.k_s] || "";
      });
    },
    onSelect(skuValue) {
      this.selectedSku =
        this.selectedSku[skuValue.skuKeyStr] === skuValue.id
          ? { ...this.selectedSku, [skuValue.skuKeyStr]: "" }
          : { ...this.selectedSku, [skuValue.skuKeyStr]: skuValue.id };
      this.matchedSku = getMatchedSku(
        this.sku.list,
        this.selectedSku,
        this.sku.tree.length
      );
      let title = "";
      this.skuTree.forEach(i => {
        if (i.k_s == skuValue.skuKeyStr) title = i.k;
      });

      this.selectedValue = this.selectedSku[skuValue.skuKeyStr]
        ? { ...this.selectedValue, [title]: skuValue.name }
        : { ...this.selectedValue, [title]: "" };

      if (this.totalStock < this.quantity) {
        this.quantity = this.totalStock;
      }

      this.$emit(
        "dataUpdate",
        this.selectedValue,
        this.matchedSku,
        this.quantity
      );
    },
    getSelectedSku() {
      return this.matchedSku ? this.matchedSku.final : null;
    }
  }
};
</script>
