<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs type="card">
          <el-tab-pane label="点餐" >
            <el-table border style="width: 100%" :data="tableData">
              <el-table-column header-align="center" prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column header-align="center" prop="count" label="数量" width="50"></el-table-column>
              <el-table-column header-align="center" prop="price" label="金额" width="70"></el-table-column>
              <el-table-column header-align="center" label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="deleteSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="total-div">
              <small>数量：</small> {{totalCount}}  &nbsp;&nbsp;&nbsp;&nbsp;  <small>金额：</small> {{ totalMoney}} 元
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="deleteAllGoods">删除</el-button>
              <el-button type="success" @click="checkOut">结账</el-button>
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
      <el-col :span="17">
        <div class="often-goods">
        <div class="title">
          常用商品
        </div>
        <div class="often-goods-list">
          <ul>
            <li v-for="(goods,index) in oftenGoods" :key="index" @click="addOrderList(goods)">
              <span>{{goods.goodsName}}</span>
              <span class="o-price">￥{{goods.price}}元</span>
            </li>
          </ul>
        </div>
        </div>

        <div class="goods-type">
          <el-tabs type="card">
            <el-tab-pane label="汉堡">
              <div>
                <ul class="cookList">
                  <li v-for="(goods,index) in type0Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class="cookList">
                <li v-for="(goods,index) in type1Goods" :key="index" @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class="cookList">
                <li v-for="(goods,index) in type2Goods" :key="index" @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class="cookList">
                <li v-for="(goods,index) in type3Goods" :key="index" @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "pos",
  created() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(response => {
        this.oftenGoods = response.data;
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，无法访问");
      });
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(response => {
        //this.oftenGoods=response.data;
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，无法访问");
      });
  },
  // 当所有dom加载完后进行操作，通过原生的方法改变dom的高度
  mounted: function() {
    let orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalMoney: 0,
      totalCount: 0
    };
  },
  methods: {
    addOrderList(goods) {
      // 每次计算都要清除
      this.totalCount = 0;
      this.totalMoney = 0;
      // 判断商品是否存在订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId === goods.goodsId) {
          isHave = true;
        }
      }
      // 根据判断的结果编写业务逻辑
      if (isHave) {
        // 改变列表中商品的数量 filter产生一个新数组，不会渲染原来数组
        let arr = this.tableData.filter(o => o.goodsId === goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      // 计算价格数量
      this.getAccount();
    },
    // 删除单个商品
    deleteSingleGoods(goods) {
      if (goods.count <= 1) {
        this.tableData = this.tableData.filter(
          o => o.goodsId !== goods.goodsId
        );
      } else {
        let arr = this.tableData.filter(o => o.goodsId === goods.goodsId);
        arr[0].count--;
      }
      this.getAccount();
    },
    deleteAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    getAccount() {
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        this.tableData.forEach(element => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + element.count * element.price;
        });
      }
    },
    // 结账
    checkOut() {
      if (this.totalCount !== 0 && this.totalMoney !== 0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        this.$message({
          message: "结账成功，感谢你，欢迎下次再来光顾！",
          type: "success",
          showClose: true,
          center: true
        });
      } else {
        this.$message({
          message: "请先添加到购物车再结账！",
          type: "error",
          showClose: true,
          center: true
        });
      }
    }
  }
};
</script>

<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
  cursor: pointer;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auto;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.total-div {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #d3dce6;
}
</style>
