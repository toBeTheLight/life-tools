<template>
  <div class="money-table">
    <template v-for="(column, index) in columns">
      <div class="money-table__column" :key="index" v-if="column.type==='fixed'">
        <span class="money-table__cell money-table__header">{{column.label}}</span>
        <span class="money-table__cell"
              v-for="(money, index) in moneyList"
              :key="index">
              {{money[column.key]}}
        </span>
      </div>
      <div class="money-table__scroll__container" :key="index" v-if="column.type=='scroll'">
        <div class="money-table__scroll">
          <div class="money-table__column"
              v-for="(subColumn, index) in column.columns"
              :key="index">
            <span class="money-table__cell money-table__header">{{subColumn.label}}</span>
            <span class="money-table__cell"
                  v-for="(money, index) in moneyList"
                  :key="index">
                  {{money[subColumn.key] || '-'}}{{subColumn.unit}}
            </span>
          </div>
        </div>
      </div>
    </template>
    <div class="money-table__column">
      <span class="money-table__cell money-table__header">操作</span>
      <span class="money-table__cell money-table__cell__action"
            v-for="(money, index) in moneyList"
            :key="money.name">
        <t-button  class="money-table__cell__btn"  @click.native="edit(index)">修改</t-button>
        <t-button  class="money-table__cell__btn" @click.native="remove(index)" type="danger">反悔</t-button>
      </span>
    </div>
    <div class="money-table__modal" v-if="editIndex >= 0" >
      <money-edit :value="moneyEditing" @change="change" @cancel="cancel"/>
      <div></div>
    </div>
  </div>
</template>

<script>
import MoneyEdit from './money-edit.vue'
import TButton from './t-button.vue'

export default {
  name: "MoneyTable",
  props: {
    value: {
      type: Array,
      default: () => [],
    }
  },
  components: {
    MoneyEdit,
    TButton
  },
  watch: {
    value: {
      immediate: true,
      handler(val) {
        this.moneyList = val.map(v => ({ ...v }));
      }
    }
  },
  data () {
    return {
      moneyList: [],
      editIndex: -1,
      editing: false,
      columns: [
        { label: "名称", key: "name", type: "fixed" },
        {
          type: "scroll",
          columns: [
            { label: "总额", key: "total", unit: '万' },
            { label: "利率", key: "rate", unit: '%' },
            { label: "期限", key: "years", unit: '年' },
            { label: "方式", key: "payment", unit: '' },
          ]
        }
      ]
    }
  },
  computed: {
    moneyEditing () {
      return this.moneyList[this.editIndex]
    }
  },
  methods: {
    remove (index) {
      this.moneyList = [
        ...this.moneyList.slice(0, index),
        ...this.moneyList.slice(index + 1)
      ]
      this.$emit('change', this.moneyList)
    },
    edit (index) {
      this.editIndex = index
      this.editing = true
    },
    change (value) {
      this.moneyList = [
        ...this.moneyList.slice(0, this.editIndex),
        value,
        ...this.moneyList.slice(this.editIndex + 1)
      ]
      this.editIndex = -1
      this.$emit('change', this.moneyList)
    },
    cancel () {
      this.editIndex = -1
    }
  }
}
</script>

<style>
.money-table {
  font-size: 14px;
}
.money-table,.money-table__scroll {
  display: flex;
  flex-direction: row;
  height: 100%;
}
.money-table__scroll__container {
  overflow-x: scroll;
  width: 100%;
  justify-content: space-around;
}
.money-table__scroll {
  display: flex;
  flex-direction: row;
  height: 100%;
  min-width: 100%;
}
.money-table__scroll .money-table__column{
  flex-grow: 1;
}
.money-table__column {
  display: flex;
  flex-direction: column;
}
.money-table__cell {
  height: 52px;
  line-height: 52px;
  flex-shrink: 0;
  padding: 2px 5px;
  word-break: keep-all;
}
.money-table__cell:nth-child(2n + 1) {
  background: #e5e5e5;
}
.money-table__header {
  height: 36px;
  line-height: 36px;
  font-weight: 700;
}
.money-table__cell__action {
  display: flex;
  line-height: 18px;
  flex-direction: column;
  justify-content: space-between;
}
.money-table__modal {
  position: fixed;
  left: 0;
  top: 0;
  z-index: 9999;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}
</style>