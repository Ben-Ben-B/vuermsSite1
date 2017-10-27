<template>
    <div class="tmpl">
        <div class="section">
            <div class="location">
                <span>当前位置：</span>
                <a href="/index.html">首页</a> &gt;
                <a href="/cart.html">购物车</a>
            </div>
        </div>

        <div class="section">
            <div class="wrapper">
                <div class="bg-wrap">
                    <!--购物车头部-->
                    <div class="cart-head clearfix">
                        <h2>
                            <i class="iconfont icon-cart"></i>我的购物车</h2>
                        <div class="cart-setp">
                            <ul>
                                <li class="first active">
                                    <div class="progress">
                                        <span>1</span>
                                        放进购物车
                                    </div>
                                </li>
                                <li>
                                    <div class="progress">
                                        <span>2</span>
                                        填写订单信息
                                    </div>
                                </li>
                                <li class="last">
                                    <div class="progress">
                                        <span>3</span>
                                        支付/确认订单
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>
                    <!--购物车头部-->
                    <!--商品列表-->
                    <div class="cart-box">
                        <input id="jsondata" name="jsondata" type="hidden">
                        <table width="100%" align="center" class="cart-table" border="0" cellspacing="0" cellpadding="8">
                            <tbody>
                                <tr>
                                    <th width="48" align="center">
                                            <el-switch
                                            v-model="isselectall"
                                            @change='changeAllSeleted'
                                            on-text="全选"
                                            off-text="反选">
                                          </el-switch>
                                    </th>
                                    <th align="left">商品信息</th>
                                    <th width="84" align="left">单价</th>
                                    <th width="104" align="center">数量</th>
                                    <th width="104" align="left">总额(元)</th>
                                    <th width="54" align="center">操作</th>
                                </tr>
                                <tr v-if='list.length<1'>
                                    <td colspan="10">
                                        <div class="msg-tips">
                                            <div class="icon warning">
                                                <i class="iconfont icon-tip"></i>
                                            </div>
                                            <div class="info">
                                                <strong>购物车没有商品！</strong>
                                                <p>您的购物车为空，
                                                    <a href="/index.html">马上去购物</a>吧！</p>
                                            </div>
                                        </div>
                                    </td>
                                </tr>
                                <tr v-for="(item,index) in list" :key="item.id">
                                   <td width="48">
                                    <el-switch
                                    v-model="values[index]"
                                    @change=' changeSeleted'
                                    on-text="已选"
                                    off-text="未选">
                                  </el-switch>
                                </td>
                                   <td align="left" >
                                       <img width="50" height="50" :src="item.img_url" alt="" style="vertical-align:middle">
                                       <span v-text="item.title" style="margin-left:20px"></span>
                                   </td>
                                   <td width="84" v-text='item.sell_price'></td>
                                   <td width="104">
                                       <myinput @update='update' :options="{gid:item.id,count:item.buycount}"></myinput>
                                   </td>
                                   <td width="104" v-text='item.sell_price * item.buycount'></td>
                                   <td width="54"><el-button type="success" size="mini" @click="deldata(item.id)">删除</el-button></td>
                                </tr>
                                <tr>
                                    <th align="right" colspan="8">
                                        已选择商品
                                        <b class="red" id="totalQuantity">{{selectCount}}</b> 件 &nbsp;&nbsp;&nbsp; 商品总金额（不含运费）：
                                        <span class="red">￥</span>
                                        <b class="red" id="totalAmount">{{selletmentAmount}}</b>元
                                    </th>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <!--/商品列表-->
                    <!--购物车底部-->
                    <div class="cart-foot clearfix">
                        <div class="right-box">
                            <button class="button" onclick="javascript:location.href='/index.html';">继续购物</button>
                            <button class="submit" onclick="formSubmit(this, '/', '/shopping.html');">立即结算</button>
                        </div>
                    </div>
                    <!--购物车底部-->
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import myinput from '../subcom/myinputNumber.vue';
    import {
        getItem,
        remoteItem
    } from '../../kits/localStorageKit.js';
    export default {
        components: {
            myinput
        },
        data() {
            return {
                selectCount: 0,
                isselectall: false,
                list: [],
                values: []
            }
        },
        created() {
            this.getList();
        },
        computed: {
            // 计算当前用户选择的商品总数量

            selletmentAmount() {
                var trueArr = this.values.filter(item => {
                    // 如果values中的值发生改变不能触发的话，就通过push和pop来改变一下数组的长度，但是不影响到数据中的原来值,在methods相关方法放
                    return item;
                });
                this.selectCount = trueArr.length;
                var totalAmount = 0;
                this.values.forEach((item, index) => {
                    if (item) {
                        var goodsInfo = this.list[index];
                        totalAmount += goodsInfo.sell_price * goodsInfo.buycount;
                    };
                });
                return totalAmount;
            }
        },
        methods: {
            update(obj) {
                this.list.forEach(item => {
                    if (item.id == obj.gid) {
                        item.buycount = obj.count;
                    };

                });

            },
            //有一个未选就反选，否则全选
            changeSeleted(cSelected) {
                //this.values.every遍历每一个值有一个是false就返回false否则返回true；
                var flag = this.values.every(item => {
                    return item;
                });
                if (flag) {
                    this.isselectall = true;
                };
                if (!cSelected) {
                    this.isselectall = false;
                };
                return 1000;
            },
            //全选或反选
            changeAllSeleted(isselectall) {
                // 遍历values将所有的值用this.isselectall赋值即可
                for (var i = 0; i < this.values.length; i++) {
                    this.values[i] = this.isselectall;
                };

            },
            deldata(goodsid) {
                var index = this.list.findIndex(item => {
                    return item.id == goodsid;
                });
                this.list.splice(index, 1);
                this.values.splice(index, 1);
                remoteItem(goodsid);
            },
            getList() {
                var goodsObj = getItem();
                var idArr = [];
                for (var key in goodsObj) {
                    idArr.push(key);
                };
                var ids = idArr.join(',');
                this.$http.get('/site/comment/getshopcargoods/' + ids).then(res => {
                    this.list = res.data.message;
                    var goodsObj = getItem();
                    this.list.forEach((item) => {
                        this.values.push(false);
                        item.buycount = goodsObj[item.id];
                    });
                });
            }
        }
    }
</script>
<style scoped>

</style>