<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs type="card">
          <el-tab-pane label="点餐" >
            <el-table show-summary border style="width: 100%" :data="tableData">
              <el-table-column header-align="center" prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column header-align="center" prop="count" label="数量" width="50"></el-table-column>
              <el-table-column header-align="center" prop="price" label="金额" width="70"></el-table-column>
              <el-table-column header-align="center" label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small">删除</el-button>
                  <el-button type="text" size="small">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger">删除</el-button>
              <el-button type="success">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单" >
            挂单
          </el-tab-pane>
          <el-tab-pane label="外卖">
            外卖
          </el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col>我是产品栏</el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'pos',
  // 当所有dom加载完后进行操作，通过原生的方法改变dom的高度
  mounted: function(){
    let orderHeight = document.body.clientHeight;
    console.log(orderHeight)
    document.getElementById('order-list').style.height = orderHeight + "px";
  },
  data() {
    return {
      tableData: [
        {
          goodsName: 'aaa',
          count: 8,
          price: '$10'
        },
        {
          goodsName: 'bbb',
          count: 8,
          price: '$10'
        },
        {
          goodsName: 'ccc',
          count: 8,
          price: '$10'
        }
      ]
    }
  }
}
</script>

<style scoped>
.pos-order{
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn{
  margin-top: 10px;
}
</style>
