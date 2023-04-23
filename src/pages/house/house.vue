<template>
  <view class="house">
    <view class="uni-form-item uni-column">
      <view class="label">总房款（万）</view>
      <input class="uni-input" type="number" v-model.number="price" focus placeholder="自动获得焦点" />
    </view>
    <view class="uni-form-item uni-column">
      <view class="label">贷款（万）</view>
      <input class="uni-input" type="number" v-model.number="loan" focus placeholder="自动获得焦点" />
    </view>
    <view class="uni-form-item uni-column"> 
      <view class="label">契税（万）</view>
      <input class="uni-input" type="number" v-model.number="deedTax" focus placeholder="自动获得焦点" />
    </view>
    <view class="uni-form-item uni-column">
      <view class="label">中介费率</view>
      <input class="uni-input" type="number" v-model="middleRate" focus placeholder="自动获得焦点" />
    </view>
    <view class="uni-form-item uni-column">
      <view class="label">总支出（万）</view>
      <view>
      房款{{price}} + 契税{{deedTax}} + 中介费{{middle}}
      </view>
      <view>
      = {{total}}万
      </view>
    </view>
    <view class="uni-form-item uni-column">
      <view class="label">首付现金（万）</view>
      <view>{{total}} - 303 = {{cash}}</view>
    </view>
    <view class="uni-form-item uni-column">
      <view class="label">现有资金（万）</view>
      <input class="uni-input" v-model="ownStr"/>
      <view>
        <text v-for="(m,i) in ownList" :key="i">
          {{m}} <text v-if="i < ownList.length - 1">+</text>
        </text>
        = {{has}}
      </view>
    </view>
    <view class="uni-form-item uni-column">
      <view class="label">缺口（万）</view>
      <view>{{cash}} - {{has}} = {{left}}</view>
    </view>
  </view>
</template>

<script>

export default {
  data () {
    return {
      price: 565,
      loan: 303,
      deedTax: 4.7,
      middleRate: 0.02025,
      ownStr: '220,44',
    }
  },
  computed: {
    middle () {
      return this.toFixed(this.price * Number(this.middleRate))
    },
    total () {
      return this.toFixed(this.price + this.deedTax + this.middle)
    },
    cash () {
      return this.toFixed(this.total - this.loan)
    },
    ownList () {
      return this.ownStr.split(',').filter(v => v).map(v => Number(v))
    },
    has () {
      return this.ownList.reduce((a, b) => a + b, 0)
    },
    left () {
      return this.toFixed(this.cash - this.has)
    }
  },
  methods: {
    toFixed (v) {
      console.log(v)
      return Number((v).toFixed(2))
    }
  }
}
</script>

<style>
.uni-input,.label {
  line-height: 20px;
  font-size: 16px;
  padding: 2px 10px;
}
.uni-input {
  margin: 2px 0;
}
.label {
  background: rgb(241, 240, 240);
}
</style>