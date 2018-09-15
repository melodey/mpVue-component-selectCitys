<template>
  <div v-show="show" class="city-container">
    <div @click.stop="close" @touchMove.stop="notDo" class="city-mask"></div>
    <div class="city-content" :class="{'show': show}">
      <div class="header-box">
        <div @click.stop="close" class="header-item button-cancel">取消</div>
        <div @click.stop="confirm" class="header-item button-confirm">确定</div>
      </div>
      <picker-view @change="cityChange" :value="cityDataValue" class="picker_box" indicator-class="selected">
        <picker-view-column>
            <view v-for="item in provinces" :key="item.value">{{item.text}}</view>
        </picker-view-column>
        <picker-view-column>
            <view v-for="item in citys" :key="item.value">{{item.text}}</view>
        </picker-view-column>
        <picker-view-column>
            <view v-for="item in countys" :key="item.value">{{item.text}}</view>
        </picker-view-column>
      </picker-view>
    </div>
  </div>
</template>

<script>
import citys from '../libs/citys.js';
// 初始化数据
let citysData = citys; // 顶级城市数据
let gProvinces = []; // 省级数据
let gCitys = []; // 市级数据
let gCountys = []; // 区县数据
export default {
  props: {
    show: Boolean,
  },
  data () {
    return {
      provinces: [], // 省级数据
      citys: [], // 市级数据
      countys: [], // 区县数据
      cityDataValue: [0, 0, 0],
    }
  },
  methods: {
    /**
     * @desc 城市选择handler
     */
    cityChange (e) {
      console.log(e.mp.detail.value, 'change');
      let _this = this;
      let values = e.mp.detail.value;
      // set data
      if (values[0] !== _this.cityDataValue[0]) {
        let citys = [];
        let countys = [];
        let _citys = citysData[values[0]].children;
        for (let j = 0, jLen = _citys.length; j < jLen; j++) {
          citys.push({
            text: _citys[j].text,
            value: _citys[j].value
          });
          if (j === 0) {
            countys = _citys[0].children;
          }
        }
        _this.citys = citys;
        _this.countys = countys;
        _this.cityDataValue = [values[0], 0, 0];
      } else if (values[1] !== _this.cityDataValue[1]) {
        _this.countys = citysData[_this.cityDataValue[0]].children[values[1]].children;
        _this.cityDataValue = [values[0], values[1], 0];
      } else if (values[2] !== _this.cityDataValue[2]) {
        _this.cityDataValue = values;
      }
      console.log(_this.cityDataValue, 'default');
    },
    /**
     * @desc confirm
     */
    confirm () {
      let _this = this;
      // 获取数据
      let provinceIndex = _this.cityDataValue[0];
      let cityIndex = _this.cityDataValue[1];
      let countyIndex = _this.cityDataValue[2];
      let data = {
        provinceName: _this.provinces[provinceIndex].text,
        provinceValue: _this.provinces[provinceIndex].value,
        cityName: _this.citys[cityIndex].text,
        cityValue: _this.citys[cityIndex].value,
        countyName: _this.countys[countyIndex].text,
        countyValue: _this.countys[countyIndex].value,
      }
      // 向上事件传递
      _this.$emit('confirm', JSON.stringify(data));
      // 还原数据
      _this.cityDataValue = [0, 0, 0];
    },
    /**
     * @desc close
     */
    close () {
      let _this = this;
      // 向上数据传递
      _this.$emit('confirm');
      // 还原数据
      _this.cityDataValue = [0, 0, 0];
    },
    /**
     * @desc 获取服务端城市数据
     */
    getCitysData () {
      let _this = this;
      // 同步获取缓存
      try {
        if (citysData && citysData.length) {
          // 初始化数据
          for (let i = 0, len = citysData.length; i < len; i++) {
            gProvinces.push({
              text: citysData[i].text,
              value: citysData[i].value
            });
            if (i === 0) {
              let _citys = citysData[0].children;
              for (let j = 0, jLen = _citys.length; j < jLen; j++) {
                gCitys.push({
                  text: _citys[j].text,
                  value: _citys[j].value
                });
                if (j === 0) {
                  gCountys = _citys[0].children;
                }
              }
            }
          }
          // set data
          _this.provinces = gProvinces;
          _this.citys = gCitys;
          _this.countys = gCountys;
        } else {
          // set data
          _this.provinces = [];
          _this.citys = [];
          _this.countys = [];
        }
      } catch (e) {
        // set data
        _this.provinces = [];
        _this.citys = [];
        _this.countys = [];
        console.log(e, 'get citys')
      }
    },
    /**
     * @desc not do
     */
    notDo () {
      // not do
    }
  },
  /**
   * @desc 数据处理
   */
  mounted () {
    this.getCitysData();
  },
  watch: {
    show: function (newVal, oldVal) {
      if (newVal !== oldVal && newVal === true) {
        if (gProvinces && gProvinces.length) {
          this.provinces = gProvinces;
          this.citys = gCitys;
          this.countys = gCountys;
        } else {
          this.getCitysData();
        }
      }
    }
  }
}
</script>

<style lang="less" scoped>
  @import '~@/less/app.less';
  @-webkit-keyframes top {
    0% {
      bottom: -282px; 
    }
    100% {
      bottom: 0;
    }
  }
  // city-container
  .city-container {
    width: 100%;
    .fx;
    bottom: 0;
    left: 0;
    top: 0;
    right: 0;
    font-size: @m_t_s;
    z-index: 999;

    >.city-mask {
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, 0.3);
    }
    >.city-content {
      width: 100%;
      background-color: #fff;
      .ab;
      bottom: -282px;
      left: 0;

      >.mask {
        background-color: rgba(0, 0, 0, 0.4);
      }
      >.header-box {
        height: 42px;
        line-height: 42px;
        overflow: hidden;
        box-sizing: border-box;
        display: flex;
        justify-content: space-between;
        border-bottom: 0.5px solid #f1f1f1; 
        
        >.header-item {
          font-size: 17px;
          color: #000;
          padding: 0 10px;
        }
        >.button-confirm {
          color: #00C534;
        }
      }
      >.selected {
        height: 45px;
      }
      >.picker_box {
        width: 100%;
        height: 240px;

        picker-view-column {
          text-align: center;
          line-height: 45px;
        }
      }
    }
    >.show {
      -webkit-animation: top 0.2s ease-in-out forwards;
    }
  }
</style>
