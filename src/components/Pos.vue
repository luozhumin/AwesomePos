<template>
    <div id="pos">
      <el-row >
        <el-col :span='7' id="order-list">
          <el-tabs v-model="activeName" >
            <el-tab-pane label="点餐" name="first">
                <el-table border style="width: 100%;text-align: left" :data="tableData" show-summary >
                  <el-table-column prop="goodsName" label="商品" ></el-table-column>
                  <el-table-column prop="count" label="数量" width="50"></el-table-column>
                  <el-table-column prop="price" label="金额" width="70"></el-table-column>
                  <el-table-column fixed="right" label="操作" width="100">
                    <template slot-scope="scope">
                      <el-button type="text" size="small" @click ="singleDel(scope.row)">删除</el-button>
                      <el-button type="text" size="small"  @click="addOrders(scope.row)">增加</el-button>
                    </template>
                  </el-table-column>
                </el-table>
              <div class="totalDiv">
                <small>数量：{{totalCount}}</small>
                <small>金额：{{totalPrice}}</small>
              </div>
                <el-row style="margin-top: 10px;">
                  <el-button type="warning">挂单</el-button>
                  <el-button type="danger" @click="delAllGoods()">删除</el-button>
                  <el-button type="success" @click="checkout">结算</el-button>
                </el-row>

            </el-tab-pane>
            <el-tab-pane label="挂单" name="second">挂单</el-tab-pane>
            <el-tab-pane label="外卖" name="third">外卖</el-tab-pane>

          </el-tabs>
        </el-col>
        <!--商品展示-->
        <el-col :span="17">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="item in oftenGoods" @click="addOrders(item)">
                  <span>{{item.goodsName}}</span>
                  <span class="o-price">{{item.price}}</span>
                </li>
              </ul>
            </div>
          </div>

          <div class="goods-type">

            <el-tabs>
              <el-tab-pane label="汉堡">
                <ul class='cookList'>
                  <li v-for="good in typeGood1" @click="addOrders(good)">
                    <span class="foodImg"><img :src="good.goodsImg" width="100%"></span>
                    <span class="foodName">{{good.goodsName}}</span>
                    <span class="foodPrice">{{good.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="小食">
                <ul class='cookList'>
                  <li v-for="good in typeGood2"  @click="addOrders(good)">
                    <span class="foodImg"><img :src="good.goodsImg" width="100%"></span>
                    <span class="foodName">{{good.goodsName}}</span>
                    <span class="foodPrice">{{good.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                <ul class='cookList'>
                  <li v-for="good in typeGood3"   @click="addOrders(good)">
                    <span class="foodImg"><img :src="good.goodsImg" width="100%"></span>
                    <span class="foodName">{{good.goodsName}}</span>
                    <span class="foodPrice">{{good.price}}</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="套餐" >
                <ul class='cookList'>
                  <li v-for="good in typeGood4"  @click="addOrders(good)">
                    <span class="foodImg"><img :src="good.goodsImg" width="100%"></span>
                    <span class="foodName">{{good.goodsName}}</span>
                    <span class="foodPrice">{{good.price}}</span>
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
  import  Axios from 'axios';
  export default {
      name: "pos",
      mounted: function () {
        var orderHeight = document.body.clientHeight;
        document.getElementById("order-list").style.height = orderHeight + 'px';
      },
       methods:{
          addOrders(good){
            let isHas = false;

            for(let i=0;i<this.tableData.length;i++){
                if(this.tableData[i].goodsId == good.goodsId){
                    isHas = true;
                    break;
                }
            }
            if(isHas){
              let arr = this.tableData.filter(o=>o.goodsId ==good.goodsId);
              arr[0].count++;
            }else{
              let new_goods = {goodsId:good.goodsId,goodsName:good.goodsName,price:good.price,count:1}
              this.tableData.push(new_goods);
            }
            this.getAllMoney();
          },
         singleDel(good){
           this.tableData = this.tableData.filter(o=>o.goodsId != good.goodsId);
           this.getAllMoney();
         },
         delAllGoods:function(){
            this.tableData = [];
            this.totalCount =0;
            this.totalPrice =0;
         },
         getAllMoney(){
           this.totalCount = 0;
           this.totalPrice = 0;
           this.tableData.forEach((element,index)=>{
             this.totalPrice+= element.count*element.price;
             this.totalCount += element.count;
           })
         },
         checkout(){
            if(this.totalCount!=0){
              this.tableData = [];
              this.totalCount =0;
              this.totalPrice =0;
              this.$message({
                message: '账成功，感谢你又为店里出了一份力!',
                type: 'success'
              });
            }else{
              this.$message({
                message: '不能空结。老板了解你急切的心情！',
                type: 'error'
              });
            }
         }
       },
      created:function(){
        Axios.get('http://jspang.com/DemoApi/oftenGoods.php').then(response =>{
        //  console.info(response);
          this.oftenGoods = response.data;
        }).catch(error=>{
          console.info("error");
        });
        Axios.get('http://jspang.com/DemoApi/typeGoods.php').then(response=>{
          this.typeGood1 = response.data[0];
          this.typeGood2 = response.data[1];
          this.typeGood3 = response.data[2];
          this.typeGood4 = response.data[3];
        }).catch(error=>{
          console.info("error")
        })
      },
      data(){
        return{
          activeName: 'first',
          totalPrice:0,
          totalCount:0,
          oftenGoods:[],
          typeGood1:[],
          typeGood2:[],
          typeGood3:[],
          typeGood4:[],
          tableData: []
        }
      }
    }
</script>

<style scoped>
  .pos {
    font-size: 12px;
  }
  .pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
  }
  .order-btn {
    margin-top: 10px;
    text-align: center;
  }
  .title {
    height: 20px;
    border-bottom: 1px solid #D3DCE6;
    background-color: #F9FAFC;
    padding: 10px;
  }
  .often-goods-list ul li {
    list-style: none;
    float: left;
    border: 1px solid #E5E9F2;
    padding: 10px;
    margin: 5px;
    background-color: #fff;
    cursor: pointer;
  }
  .goods-type {
    clear: both;
  }
  .o-price {
    color: #58B7FF;
  }
  .often-goods-list {
    border-bottom: 1px solid #C0CCDA;
    height: auto;
    overflow: hidden;
    padding-bottom: 10px;
    background-color: #F9FAFC;
  }
  .cookList li {
    list-style: none;
    width: 23%;
    border: 1px solid #E5E9F2;
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
    font-size: 18px;
    padding-left: 10px;
    color: brown;
  }
  .foodPrice {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
  }
  .totalDiv {
    height: auto;
    overflow: hidden;
    text-align: right;
    font-size: 16px;
    background-color: #fff;
    border-bottom: 1px solid #E5E9F2;
    padding: 10px;
  }
</style>
