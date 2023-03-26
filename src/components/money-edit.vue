<template>
  <div class="money-edit">
    <div class="money-edit__field">
      <label class="money-edit__label">名称：</label>
      <input class="money-edit__input" type="text" v-model="money.name" />
    </div>
    <div class="money-edit__field">
      <label class="money-edit__label">总额：</label>
      <input  class="money-edit__input" type="number" v-model="money.total" />
    </div>
    <div class="money-edit__field">
      <label class="money-edit__label">利率：</label>
      <span class="money-edit__input" >
        <input type="number" v-model="money.rate" />%
      </span>
    </div>
    <div class="money-edit__field">
      <label class="money-edit__label">期数：</label>
      <span class="money-edit__input" >
       <input type="number" v-model="money.years" />年
      </span>
    </div>
    <div class="money-edit__field">
      <label class="money-edit__label">
        方式：
      </label>
      <picker :value="paymentIndex" @change="paymentChange" class="money-edit__input" :range="paymentOptions" range-key="value">
          {{ money.payment }}
      </picker>
    </div>
    <div class="money-edit__btn-wrapper">
      <t-button @click.native="cancel" class="money-edit__btn-cancel">取消</t-button>
      <t-button type="primary" @click.native="change">确定</t-button>
    </div>
  </div>
</template>

<script>
import TButton from "./t-button.vue";

export default {
  name: "MoneyEdit",
  props: {
    value: {
      type: Object,
      default: () => ({}),
    },
  },
  components:{
    TButton
  },
  data() {
    return {
      money: {
        name: "",
        total: "",
        rate: "",
        payment: "",
        years: "",
      },
      paymentOptions: [
        { label: "等额本息", value: "等额本息" },
        { label: "等额本金", value: "等额本金" },
        { label: "月息年金", value: "月息年金" },
        { label: "一次性本金", value: "一次性本金" },
        { label: "一次性利息", value: "一次性利息" },
      ]
    }
  },
  watch: {
    value: {
      immediate: true,
      handler(val) {
        this.money = { ...val };
      }
    }
  },
  computed: {
    paymentIndex () {
      return this.paymentOptions.map(i => i.value).indexOf(this.value.payment)
    }
  },
  methods: {
    paymentChange (event) {
      this.money.payment = this.paymentOptions[event.detail.value].value
    },
    change() {
      this.$emit("change", {
        name: this.money.name,
        total: this.money.total ? Number(this.money.total) : 0,
        rate: this.money.rate ? Number(this.money.rate) : 0,
        payment: this.money.payment,
        years: this.money.years ? Number(this.money.years) : 0,
      })
    },
    cancel () {
      this.$emit("cancel")
    }
  }
}
</script>

<style>
.money-edit {
  background: white;
  padding: 20px;
  line-height: 20px;
  flex-grow: 0;
  width: 80%;
}
.money-edit__field {
  display: flex;
  line-height: 20px;
  margin-bottom: 10px;
}
.money-edit__label {
  flex-grow: 0;
  line-height: 22px;
  word-break: keep-all;
}
.money-edit__input {
  display: flex;
  width: 100%;
  border-bottom: 1px solid black;
  justify-content: space-between;
}
.money-edit__btn-wrapper {
  text-align: right;
}
.money-edit__btn-cancel {
  margin-right: 8px;
}
</style>