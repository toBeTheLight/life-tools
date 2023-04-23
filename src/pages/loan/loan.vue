<template>
  <view class="content">
    <div class="content__section">
      <div class="content-title content-info">
        款项
        <div class="money-list__action">
          <t-button type="primary" @click.native="add">新增</t-button>
        </div>
      </div>
      <div class="money-list">
        <money-table :value="moneyList" @change="change"/>
      </div>
    </div>

    <div class="content__section">
      <div>
        <div class="content-title">合计</div>
        <div>总支出：{{ finallyTotal.total | toFixed }}</div>
        <div>总利息：{{ finallyTotal.interest | toFixed }}</div>
        <div>总本金：{{ finallyTotal.principal | toFixed }}</div>
      </div>
    </div>
    <div class="content__section">
      <div class="content-title">月度还款</div>
      <div class="content-detail__tabs">
        <div class="content-detail__tab"
            :class="{'content-detail__tab--active': activeDetail === detail.value}"
              v-for="detail in detailList"
              :key="detail.value"
              @click="() => activeDetail = detail.value" >
          {{detail.name}}
        </div>
      </div>
      <div class="content-detail__table" v-if="activeDetail === 'total'">
        <div class="content-detail__table-tr">
          <div  class="content-detail__table-td">月</div>
          <div class="content-detail__table-td">月付</div>
          <div class="content-detail__table-td">利息</div>
          <div class="content-detail__table-td">本金</div>
        </div>
        <div v-for="(sum, index) in sumPays" :key="index"  class="content-detail__table-tr">
          <div class="content-detail__table-td">{{months[index]}}</div>
          <div class="content-detail__table-td">{{ Number(sum.pay.toFixed(4)) }}</div>
          <div class="content-detail__table-td">{{ Number(sum.interest.toFixed(4)) }}</div>
          <div class="content-detail__table-td">{{ Number(sum.principal).toFixed(4) }}</div>
        </div>
      </div>
      <div class="content-detail__table" v-if="detailPay">
        <div  class="content-detail__table-tr">
          <div class="content-detail__table-td">月付</div>
          <div class="content-detail__table-td">利息</div>
          <div class="content-detail__table-td">本金</div>
        </div>
        <div v-for="(month, index) in detailPay" :key="index"  class="content-detail__table-tr">
          <div class="content-detail__table-td">{{ month.pay | toFixed }}</div>
          <div class="content-detail__table-td">{{ month.interest | toFixed }}</div>
          <div class="content-detail__table-td">{{ month.principal | toFixed }}</div>
        </div>
      </div>
    </div>
  </view>
</template>

<script>
import MoneyTable from '../../components/money-table.vue'
import TButton from '../../components/t-button.vue'

export default {
  data() {
    return {
      activeDetail: 'total',
      moneyList: [
        {
          name: "首付",
          total: 260,
          payment: "一次性本金",
        },
        {
          name: "商业房贷",
          total: 210,
          rate: 4.85,
          years: 25,
          payment: "等额本息",
        },
        {
          name: "公积金房贷",
          total: 60,
          rate: 3.1,
          years: 25,
          payment: "等额本息",
        },
        {
          name: "中介费",
          total: 570,
          rate: 2,
          payment: "一次性利息",
        },
        {
          name: "契税",
          total: 570,
          rate: 1,
          payment: "一次性利息",
        },
        {
          name: "其他借款",
          total: 30,
          rate: 3,
          years: 3,
          payment: "月息年金",
        },
      ],
    };
  },
	components: {
    MoneyTable,
    TButton
	},
  filters: {
    toFixed(value) {
      if (!value) return "-";
      try {
        return Number(value.toFixed(4));
      } catch (error) {
        console.log(value);
      }
    },
  },
  computed: {
    months () {
      const date = new Date()
      const year = new Date(date).getFullYear()
      const month = new Date(date).getMonth() + 1
      return new Array(this.maxMonths).fill(0).map((v, index) => {
        let m = `${(month + index) % 12}`
        const y = Math.floor((month + index)/13) + year
        if (m === '0') m = '12'
        if (m.length === 1) m = `0${m}`
        return `${y}/${m}`
      })
    },
    detailList () {
      return [
        {
          value: 'total',
          name: '月度合计'
        },
        ...new Array(this.totalPays.length).fill(0).map((v, index) => ({
          value: index,
          name: this.moneyList[index].name
        }))
      ]
    },
    detailPay () {
      return this.totalPays[this.activeDetail]
    },
    finallyTotal() {
      return {
        total: this.sumBy(this.sumPays, "pay"),
        interest: this.sumBy(this.sumPays, "interest"),
        principal: this.sumBy(this.sumPays, "principal"),
      };
    },
    sumPays() {
      return new Array(this.maxMonths).fill(0).map((n, index) => {
        return {
          pay: this.sumBy(this.totalPays, (part) => part?.[index]?.pay || 0),
          interest: this.sumBy(this.totalPays, (part) => part?.[index]?.interest || 0 ),
          principal: this.sumBy(this.totalPays, (part) => part[index]?.principal || 0)
        };
      });
    },
    totalPays() {
      return this.moneyList.map((money, index) => {
        if (money.payment === "等额本息") return this.equalInterest(money);
        if (money.payment === "一次性本金") return [{ pay: money.total }];
        if (money.payment === "一次性利息")
          return [{ pay: money.total * (money.rate / 100) }];
        if (money.payment === "月息年金") return this.interestFirst(money);
        return [];
      });
    },
    maxMonths() {
			return this.totalPays.map((part) => part.length).reduce((a, b) => Math.max(a, b), 0)
    },
  },
  methods: {
		sumBy (array, iteratee) {
			let result = 0
			for (let i = 0; i < array.length; i++) {
				result += typeof iteratee === 'string' ? array[i][iteratee] : iteratee(array[i])
			}
			return result
		},
    add() {
      this.moneyList.push({});
    },
    remove(index) {
      this.moneyList.splice(index, 1);
    },
    change(value) {
      this.moneyList = [...value];
    },
    equalInterest(money) {
      const { total, rate, years } = money;
      const months = years * 12;
      const ratePerMonth = rate / (12 * 100);
      let surplus = total;
      const result = [{ pay: 0, interest: 0, principal: 0 }];
      while (surplus > 0) {
        const monthPay =
          (total * (ratePerMonth * Math.pow(1 + ratePerMonth, months))) /
          (Math.pow(1 + ratePerMonth, months) - 1);
        const monthInterest = surplus * ratePerMonth;
        const monthPrincipal = monthPay - monthInterest;
        surplus -= monthPrincipal;
        result.push({
          pay: monthPay,
          interest: monthInterest,
          principal: monthPrincipal,
        });
      }
      return result;
    },
    interestFirst(money) {
      const { total, rate, years } = money;
      const months = years * 12;
      const ratePerMonth = rate / (12 * 100);
      let surplus = total;
      const result = [{ pay: 0, interest: 0, principal: 0 }];
      let n = 1;
      while (surplus > 0) {
        const monthInterest = surplus * ratePerMonth;
        let principal = 0;
        if (n % 12 === 0) {
          principal = total / years;
        }
        const monthPay = monthInterest + principal;
        surplus -= principal;
        n += 1;
        result.push({
          pay: monthPay,
          interest: monthInterest,
          principal: principal,
        });
      }
      return result;
    },
  },
};
</script>

<style>
.content__section {
  margin-bottom: 20px;
}
.content-info {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  margin-bottom: 10px;
}
.content-title {
  font-size: 22px;
  line-height: 32px;
  margin: 5px 0;
  text-indent: 10px;
  border-left: 3px solid #18a0fb;
}
.money-list__action {
  text-align: right;
  align-items: center;
  text-indent: 0;
  display: flex;
  padding-right: 5px;
}
.content-detail__table {
  display: table;
  width: 100%;
}
.content-detail__table-tr {
  display: table-row;
  width: 100%;
}
.content-detail__table-td {
  display: table-cell;
  padding: 5px 10px;
  border: 1px solid #ddd;
}
.content-detail__tabs {
  overflow-x: scroll;
  white-space: nowrap;
  padding-bottom: 5px;
}
.content-detail__tab {
  display: inline-block;
  margin-right: 10px;
  padding: 10px;
}
.content-detail__tab--active {
  border-bottom: 3px solid #18a0fb;
}
</style>
