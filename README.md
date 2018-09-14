# mpVue-component-selectCitys
基于mpVue的小程序城市选择三级联动

## 城市数据（因小程序包体积限制，建议城市数据放在服务器）
```json
[{
  "value":2, // 省直辖市
  "text":"北京市",
  "children":[{
    "value":52, // 市
    "text":"北京",
    "children":[{
      "value":500, // 区县
      "text":"东城区"
    }]
  }]
}]
```
## 调用
 *组件方式引入
 ```js
 import city from '@/components/selectCity'
 ```
 *组件调用
 ```js
 <city @confirm="regionChange"></city>
 ```
*事件回调
```js
  regionChange (data) {
    this.showCity = false;
    if (!data) {
      return;
    }
    // 处理城市数据
    let cityData = JSON.parse(data);
    // 数据展示
    ...
  }
```
