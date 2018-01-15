
<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width: 100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="100"></el-table-column>
              <el-table-column prop="price" label="金额" width="100"></el-table-column>
              <el-table-column label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div>
              <span v-text="'数量:'+ totalCount"></span><span v-text="'金额：'+ totalMoney + '元'"></span>
            </div>
            <div class="btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger">删除</el-button>
              <el-button type="success">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul >
              <li v-for="(items,index) in oftenGoods" :key="index" @click="addOrderList(items)">
                <span v-text="items.goodsName"></span>
                <span class="o-price" v-text="'￥'+items.price">15元</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div class="cookList">
                <ul>
                  <li v-for="(goods, index) in type0Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" :alt="goods.goodsName"></span>
                    <span class="foodName" v-text="goods.goodsName"></span><br>
                    <span class="foodPrice" v-text="'￥'+ goods.price"></span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div class="cookList">
                <ul>
                  <li v-for="(goods, index) in type1Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" :alt="goods.goodsName"></span>
                    <span class="foodName" v-text="goods.goodsName"></span><br>
                    <span class="foodPrice" v-text="'￥'+ goods.price"></span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div class="cookList">
                <ul>
                  <li v-for="(goods, index) in type2Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" :alt="goods.goodsName"></span>
                    <span class="foodName" v-text="goods.goodsName"></span><br>
                    <span class="foodPrice" v-text="'￥'+ goods.price"></span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div class="cookList">
                <ul>
                  <li v-for="(goods, index) in type3Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" :alt="goods.goodsName"></span>
                    <span class="foodName" v-text="goods.goodsName"></span><br>
                    <span class="foodPrice" v-text="'￥'+ goods.price"></span>
                  </li>
                </ul>
              </div>
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
  created() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(reponse => {
        this.oftenGoods = reponse.data;
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      });

    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(reponse => {
        this.type0Goods = reponse.data[0];
        this.type1Goods = reponse.data[1];
        this.type2Goods = reponse.data[2];
        this.type3Goods = reponse.data[3];
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      });
  },
  mounted() {
    var orderHeight = document.body.clientHeight;
    console.log(orderHeight);
    document.querySelector("#order-list").style.height = orderHeight + "px";
  },
  methods: {
    addOrderList(goods) {
      this.totalMoney = 0;
      this.totalCount = 0;
      //判断商品在不在订单列表
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据判断的值编写业务逻辑
      if (isHave) {
        //改变商品数量
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
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
    },
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
    },
    getAllMoney() {
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        this.tableData.forEach(element => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + element.price * element.count;
        });
      }
    }
  }
};
</script>
<style lang="stylus" scoped>

.pos-order {
  background-color: #f9fafc;
  height: 100%;
}

.btn {
  margin-top: 10px;
}

.title {
  height: 20px;
  border-bottom: 1px solid #ffffff;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}

.goods-type {
  background-color: #ffffff;
}

.often-goods-list {
  background-color: #ffffff;

  ul {
    padding: 0;
    margin: 0;
    text-align: left;

    li {
      display: inline-block;
      list-style: none;
      border: 1px solid #f9fafc;
      background-color: #ffffff;
      padding: 10px;
      margin: 10px;
      cursor: pointer;

      .o-price {
        color: #58b7ff;
      }
    }
  }
}

.cookList {
  ul {
    text-align: left;
    padding: 0;
    margin: 0;
    display: flex;
    flex-wrap: wrap;

    li {
      width: 20%;
      list-style: none;
      border: 1px solid #f9fafc;
      background-color: #ffffff;
      padding: 10px;
      margin: 10px;
      cursor: pointer;

      span {
        display: block;
      }

      .foodImg {
        width: 40%;
        float: left;
        margin-right: 10px;

        img {
          width: 100%;
        }
      }

      .foodName {
        font-size: 18px;
        color: brown;
      }

      .foodPrice {
        font-size: 16px;
      }
    }
  }
}
</style>
