# sku-select
sku选择组件

## install

encourage using [yarn](https://yarnpkg.com/en/docs/install#mac-stable) to install

```sh
yarn add sku-select
```

## usage

### template

```vue
<template>
  <sku-select ref="sku"
              @dataUpdate="onSelectSku"
              :sku="sku" />
</template>
```
### script

```vue
import SkuSelect from 'sku-select/SkuSelect'
import SkuStepperStock from "@/components/sku/sku-stepper-stock";
export default {
  components: {
    SkuSelect,
    SkuStepperStock, //数量选择
  },
data: {
    return {
        resultSku: {},
        assistForm: {},
        sku: { list: [], tree: [] },
        quantity: 1,
        maxStock: 0,
        skuPrice: null,
    }
},
  methods: {
	onSelectSku(selectedSku, final) {
      this.assistForm = { ...selectedSku };

      if (final && final.final) {
        this.resultSku = final.final;
        this.skuPrice = final.final.price;
      } else {
        this.resultSku = {};
        this.skuPrice = null;
      }
      this.maxStock = this.resultSku.stock_num;
    }
}
}
```
## sku数据格式例子

```js
sku: { list: [{
		"skuId": "123",
		"guidePrice": null,
		"sellPrice": "11",
		"photoUrl": "",
		"stockUnit": null,
		"propIds": ["1234"], //规格组
		"stockCount": "15",
		"status": null,
		"propNames": null,
		"propValues": null,
		"originalPrice": null,
    	"id": "1111", //必须字段
		"price": "11",  //必须字段
		"stock_num": "15", //必须字段
		"s1": "1234" //规格id， 必须字段
	}], tree: [{ //tree中都是必须字段
		"k": "净含量", //规格name
		"v": [{
			"id": "", //规格id
			"name": "8.5 OZ.(250ML)", //规格对应的key
			"attrId": "ea5fdbdf40814b1b9e0beb866873f28b" //规格id
		}],
		"k_s": "s1"
	}] }
```
