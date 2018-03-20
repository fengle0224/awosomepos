<template >
    <div class="pos">
       <el-row >
            <el-col :span='7' class="pos-order" id="order-list">
                <el-tabs>
                      <el-tab-pane label="点餐">
                        <el-table :data="tableData" border  style="width:100%;"> 
                          <el-table-column prop="goodsName" label="商品名称">
                          </el-table-column>
                          <el-table-column prop="count" label="数量">
                          </el-table-column>
                          <el-table-column prop="price" label="金额">
                          </el-table-column>
                          <el-table-column label="操作" width="100" fixed="right">
                            <template scope="scope">
                              <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                              <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                            </template>
                          </el-table-column>
                        </el-table>
                        <div class="all">
                          <small>数量：</small>{{totalCount}} &nbsp;&nbsp;&nbsp; <small>价格：</small>{{totalPrice}}元
                        </div>
                        <div class="div-btn">
                          <el-button type="warning">挂单</el-button>
                          <el-button type="danger" @click="delAllGoods()">删除</el-button>
                          <el-button type="success" @click="checkout()">结账</el-button>
                         </div>
                      </el-tab-pane>
                      <el-tab-pane label="挂单">
                      挂单
                      </el-tab-pane>
                      <el-tab-pane label="外卖">
                      外卖
                    </el-tab-pane>
                </el-tabs>
            </el-col>
            <!--商品展示-->
            <el-col :span='17'>
            <div class="often-goods">
              <div class="title">常用商品</div>
              <div class="often-goods-list">
                <ul>
                  <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                    <span>{{goods.goodsName}}</span>
                    <span class="o-price">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </div>
             <div class="goods-type">
              <el-tabs>
                <el-tab-pane label="汉堡">
                  <ul class="cookList">
                    <li v-for="goods in type0Goods"  @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="小食" >
                  <ul class="cookList">
                    <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="饮料" >
                  <ul class="cookList">
                    <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </el-tab-pane>
                <el-tab-pane label="套餐"  >
                  <ul class="cookList">
                    <li v-for="goods in type3Goods" @click="addOrderList(goods)">
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
import axios from 'axios';
export default {
  name: 'pos',
  data(){
    return {
      tableData: [],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalCount:0,
      totalPrice:0
    }
  },
  created:function(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(response=>{
      this.oftenGoods=response.data
    })
    .catch(error=>{
      alert("网络错误，不能访问");
    })

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(response=>{
         this.type0Goods=response.data[0];
         this.type1Goods=response.data[1];
         this.type2Goods=response.data[2];
         this.type3Goods=response.data[3];
    })
    .catch(error=>{
      console.log(error);
      alert("网络错误，不能访问");
    })
  },
  // 当所有的虚拟DOM完成之后的方法
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    document.getElementById('order-list').style.height=orderHeight+'px';

  },
  methods:{
    // 增加商品至点餐列表
    addOrderList(goods){
     
      // 判断这个商品是否已存在于列表中
      let isHave=false;
      for(let i=0;i<this.tableData.length;i++){
        if(this.tableData[i].goodsId==goods.goodsId){
          isHave=true;
        }
      } 
      if(isHave){
      // 存在就进行数量添加
        let arr=this.tableData.filter(o=>o.goodsId==goods.goodsId);
        // console.log(arr);
        arr[0].count++;  
      } else{
      // 不存在就压入数组
       let newgood={goodsId:goods.goodsId,goodsName:goods.goodsName,count:1,price:goods.price};
       this.tableData.push(newgood);
      }
      this.allMoney();   
      
    },
    //删除单个商品
    delSingleGoods(goods){
      console.log(goods);
      this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
      this.allMoney();   
    },
    //删除所有商品
    delAllGoods(){
       this.totalCount=0;
       this.totalPrice=0;
       this.tableData=[];
    },
    //汇总数量和金额
    allMoney(){
       this.totalCount=0;
       this.totalPrice=0;
       // 进行价格和数量的汇总
       this.tableData.forEach((element)=>{
         this.totalCount+=element.count;
         this.totalPrice+=element.count*element.price;
      })
    },
    //模拟结账功能
    checkout(){
      if(this.totalCount!=0){
        this.totalCount=0;
        this.totalPrice=0;
        this.tableData=[];
        this.$message({
          message:"付款成功,恭喜您又为本店出了一份力！",
          type:"success"
        })
      }else{
        this.$message.error("不能空结");
      }
    }
  },
  beforeCreate:function(){
    console.log('1-beforeCreate 初始化之后');
  },
  beforeMount:function(){
    console.log('3-beforeMount 挂载之前');
  },
  beforeUpdate:function(){
    console.log('5-beforeUpdate 数据更新前');
  },
  updated:function(){
    console.log('6-updated 被更新后');
  },
  activated:function(){
    console.log('7-activated');
  },
  deactivated:function(){
    console.log('8-deactivated');
  },
  beforeDestroy:function(){
    console.log('9-beforeDestroy 销毁之前');
  },
  destroyed:function(){
    console.log('10-destroyed 销毁之后')
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .pos-order{
    background-color:#f9fafc;
    border-right:1px solid #c0ccda;
    height:100%;
  }
  .div-btn{
    margin-top:10px;
    text-align:center;
  }
   .title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
   }
   .often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #E5E9F2;
      padding:10px;
      margin:5px;
      background-color:#fff;
      cursor:pointer;
   }
  .o-price{
      color:#58B7FF; 
   }
   .goods-type{
     clear:both;
   }
   .cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor:pointer;
   }
   .cookList li span{
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .all{
     text-align:center;
     padding:10px 0;
     border-bottom:1px solid #E5E9F2;
   }
</style>
