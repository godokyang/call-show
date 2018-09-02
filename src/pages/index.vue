
<!--  -->
<template>
  <div class="main">
    <div class="title">
      {{profile.projectName}}
      <div class="detail-info">
        执行日期: {{profile.date}} <br> 执行人员: {{profile.tester}}
      </div>
    </div>
    <div class="total-area">
      <div class="total-cld" style="border-right: 1px solid #999999;">{{profile.description}}</div>
      <div class="total-cld total-rate">
        <div class="part1">
          执行总数: {{profile.total}} <br>
          执行成功: {{profile.successTotal}} <br>
          执行失败: {{profile.failTotal}} <br>
          等待测试: {{profile.penddingTotal}}
        </div>
        <div class="part2" id="myEcharts"></div>
      </div>
    </div>
    <div class="detail-area">
      <div class="select-area">
        <ul class="select-item">
          <li class="select-li" v-bind:class="{ 'success': val.status === 'success' , 'failed': val.status === 'failed', 'pendding': val.status === 'pendding' }" v-on:click="selectCategories(key, false)" v-for="(val, key) in categories">{{key}}</li>
        </ul>
        <ul v-if="subCategories && subCategories.detail" class="select-item">
          <li class="select-li" v-bind:class="{ 'success': val.status === 'success' , 'failed': val.status === 'failed', 'pendding': val.status === 'pendding' }" v-on:click="selectCategories(key, true)" v-for="(val, key) in subCategories.detail">{{key}}</li>
        </ul>
      </div>
      <div class="show-area">
        <ul v-if="detail && key !== 'hasSub'" v-for="(val, key) in detail" class="show-item">
          <li class="detailKey">{{key}}</li>
          <li class="detailVal">{{val}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import dtoData from '@/service/dataService.js'

export default {
  name: 'index',
  data () {
    return {
      profile: dtoData.profile,
      categories: dtoData.modelDetail,
      subCategories: null,
      detail: null
    }
  },
  components: {},

  computed: {},

  mounted () {
    console.log(this)
    this.drawLine()
  },

  methods: {
    selectCategories (key, isSub) {
      if (isSub) {
        this.detail = this.subCategories.detail[key]
      } else {
        this.subCategories = this.categories[key].hasSub ? this.categories[key] : null
        this.detail = this.categories[key].hasSub ? this.subCategories.detail[key] : this.categories[key]
      }
    },
    drawLine () {
    // 基于准备好的dom，初始化echarts实例
      let myChart = this.$echarts.init(document.getElementById('myEcharts'))
      // 绘制图表
      let option = {
        title: {
          text: '测试进度统计',
          left: 'center',
          textStyle: {
            color: '#333333',
            fontSize: '15px'
          }
        },
        tooltip: {
        },
        legend: {
        // orient: 'vertical',
        // top: 'middle',
          bottom: 10,
          left: 'center',
          data: ['成功', '失败', '待测']
        },
        series: [
          {
            type: 'pie',
            radius: '55%',
            center: ['50%', '50%'],
            selectedMode: 'single',
            itemStyle: {
              normal: {
                label: {
                  show: false // 隐藏标示文字
                },
                labelLine: {
                  show: false // 隐藏标示线
                }
              }
            },
            data: [
              {value: this.profile.successTotal, name: '成功', itemStyle: {color: '#92c349'}},
              {value: this.profile.failTotal, name: '失败', itemStyle: {color: '#cf4341'}},
              {value: this.profile.penddingTotal, name: '待测', itemStyle: {color: '#457ec4'}}
            ]
          }
        ]

      }

      myChart.setOption(option)
    }
  }
}

</script>
<style lang='scss' scoped>
ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
.main{
  min-width: 800px;
  position: relative;
  .title{
    height: 50px;
    width: 100%;
    text-align: center;
    line-height: 50px;
    font-size: 20px;
    color: #333333;
    position: relative;
    background-color: aqua;
    .detail-info {
      // float: right;
      position: absolute;
      display: inline-block;
      line-height: 14px;
      text-align: left;
      font-size: 10px;
      color: #999999;
      right: 0;
      bottom: 0;
    }
    .detail-info:after{
      clear:both;
    }
  }
  .total-area{
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    border-bottom: 1px solid #999999;
    min-height: 100px;
    .total-cld{
      flex-grow: 1;
      width:0;
      font-size: 13px;
      color: #333333;
      text-align: left;
      padding: 5px 15px;
    }
    .total-rate{
      display: flex;
      flex-direction: row;
      .part1{
        width: 0;
        flex-grow: 1
      }
      .part2{
        min-width: 200px;
        min-height: 200px;
      }
    }
  }
  .detail-area{
    display:flex;
    flex-direction: row;
    .select-area{
      display:flex;
      flex-direction: row;
      width:0;
      flex-grow: 1;
      .select-item{
        flex-grow: 1;
        width: 0;
        font-size: 15px;
        color: #ffffff;
        text-align: left;
        border-bottom: 1px solid #999999;
        border-right: 1px solid #999999;
        .select-li{
          padding: 3px 20px;
          border-bottom: 1px solid #999999;
        }
      }
    }
    .show-area{
      display:flex;
      flex-direction: column;
      width:0;
      flex-grow: 1;
      .show-item{
        display:flex;
        flex-direction: row;
        border-bottom: 1px solid #999999;
        border-right: 1px solid #999999;
        li{
          width: 0;
          padding: 5px 20px 5px 10px;
          font-size: 12px;
          color: #666666;
          text-align: left;
        }
        .detailKey{
          flex-grow: 1;
          background-color: aqua;
        }
        .detailVal{
          flex-grow: 2;
        }
      }
    }
  }
  .success{
    background-color: #92c349;
  }
  .success::after{
    content: 'success';
    padding-left: 50px;
    font-size: 7px;
  }
  .failed{
    background-color: #cf4341;
  }
  .failed::after{
    content: 'failed';
    padding-left: 50px;
    font-size: 7px;
  }
  .pendding{
    background-color: #457ec4;
  }
  .pendding::after{
    content: 'pendding';
    padding-left: 50px;
    font-size: 7px;
  }
}
</style>
