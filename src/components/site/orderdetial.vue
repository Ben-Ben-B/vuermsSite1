<template>
    <div class="tmpl" v-if='orderD.orderinfo'> 
            <div class="sub-tit">
                <a href="javascript:void(0)" class="add" @click="go">
                    <i class="iconfont icon-reply"></i>返回</a>
                <ul>
                    <li class="selected">
                        <a href="javascript:;">查看订单</a>
                    </li>
                </ul>
            </div>
            <div class="order-progress" >
                <ul>
                    <li v-bind="{class:'first '+ (status>=1?'active':'')}">
                        <div class="progress">下单</div>
                        <div class="info">{{orderD.orderinfo.add_time | datefmt('YYYY-MM-DD hh:mm:ss')}}</div>
                    </li>
                    <li v-bind="{class:status>=2?'active':''}">
                        <div class="progress">已付款</div>
                        <div class="info">{{orderD.orderinfo.confirm_time | datefmt('YYYY-MM-DD hh:mm:ss')}}</div>
                    </li>
                    <li v-bind="{class:status>=3?'active':''}">
                        <div class="progress">已经发货</div>
                        <div class="info">{{orderD.orderinfo.express_time | datefmt('YYYY-MM-DD hh:mm:ss')}}</div>
                    </li>
                    <li v-bind="{class:'last '+ (status>=4?'active':'')}">
                        <div class="progress">已完成</div>
                        <div class="info">{{orderD.orderinfo.complete_time | datefmt('YYYY-MM-DD hh:mm:ss')}}</div>
                    </li>
                </ul>
            </div>
            <div class="form-box accept-box form-box1">
                <dl class="head form-group">
                    <dd>
                        订单号：{{orderD.orderinfo.order_no}}

                        <router-link class="btn-pay" v-if="orderD.orderinfo.status<=1" v-bind="{to:'/site/pay/'+orderD.orderinfo.id}">去付款</router-link>
                        <a  class="btn-pay" v-if="orderD.orderinfo.status==3" @click="complate(orderD.orderinfo.id)">待签收</a>
                    </dd>
                </dl>
                <dl class="form-group">
                    <dt>订单状态：</dt>
                    <dd v-text='orderD.orderinfo.statusName'>
                       
                    </dd>
                </dl>
                <dl class="form-group">
                    <dt>快递单号：</dt>
                    <dd v-text='orderD.orderinfo.express_no'>

                    </dd>
                </dl>
                <dl class="form-group">
                    <dt>支付方式：</dt>
                    <dd v-text='orderD.orderinfo.paymentTitle'></dd>
                </dl>
            </div>
            <div class="table-wrap">
                <table width="100%" border="0" cellspacing="0" cellpadding="5" class="ftable">
                    <tbody>
                        <tr>
                            <th align="left" colspan="2">商品信息</th>
                            
                            <th width="10%">单价
                            </th>
                            <th width="10%">数量</th>
                            <th width="10%">金额</th>
                        </tr>
                        <tr v-for='item in orderD.goodslist' :key="item.id">
                            <td width="60">
                                    <router-link v-bind='{to:"/site/goodsinfo/"+item.goods_id}'>
                                <img :src="item.imgurl" class="img">
                            </router-link>
                            </td>
                            <td align="left">
                                <router-link v-bind='{to:"/site/goodsinfo/"+item.goods_id}'>{{item.goods_title}}</router-link>
                            </td>
                            <td align="center">
                                <s>￥{{item.goods_price}}</s>
                                <p>￥{{item.real_price}}</p>
                            </td>
                            <td align="center">{{item.quantity}}</td>
                            <td align="center">￥{{item.quantity*item.real_price}}</td>
                        </tr>
                        <tr>
                            <td colspan="7" align="right">
                                <p>商品金额：
                                    <b class="red">￥{{totalamount}}</b>&nbsp;&nbsp;+&nbsp;&nbsp;运费：
                                    <b class="red">￥{{orderD.orderinfo.express_fee}}</b>
                                </p>
                                <p style="font-size: 22px;">应付总金额：
                                    <b class="red">￥{{totalamount+orderD.orderinfo.express_fee}}</b>
                                </p>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="form-box accept-box">
                <dl class="head form-group">
                    <dd>收货信息</dd>
                </dl>
                <dl class="form-group">
                    <dt>顾客姓名：</dt>
                    <dd v-text='orderD.orderinfo.accept_name'></dd>
                </dl>
                <dl class="form-group">
                    <dt>送货地址：</dt>
                    <dd>{{orderD.orderinfo.area}} {{orderD.orderinfo.address}}</dd>
                </dl>
                <dl class="form-group">
                    <dt>联系电话：</dt>
                    <dd v-text='orderD.orderinfo.mobile'> </dd>
                </dl>
                <dl class="form-group">
                    <dt>电子邮箱：</dt>
                    <dd v-text='orderD.orderinfo.email'> </dd>
                </dl>
                <dl class="form-group">
                    <dt>备注留言：</dt>
                    <dd v-text='orderD.orderinfo.message'></dd>
                </dl>
            </div>
        </div>
 
</template>

<script>
    export default {
        data() {
            return {
                orderid: this.$route.params.orderid,
                orderD: {},
                status: 1,
                totalamount: 0
            }
        },
        created() {
            this.getorderDetial();
        },
        methods: {
            //签收页面
            complate(id) {
                this.$http.get('/site/validate/order/complate/' + id).then(res => {
                    if (res.data.status == 1) {
                        this.$message.error(res.data.message)
                    }
                    this.getorderDetial();
                });
            },
            go() {
                this.$router.go(-1)
            },
            getorderDetial() {
                this.$http.get('/site/validate/order/getorderdetial/' + this.orderid).then(res => {
                    if (res.data.status == 1) {
                        this.$message.error(res.data.message);
                    }
                    this.orderD = res.data.message;
                    this.status = res.data.message.orderinfo.status;
                    for (var i = 0; i < res.data.message.goodslist.length; i++) {
                        var item = res.data.message.goodslist[i];
                        this.totalamount += (item.real_price * item.quantity);
                    }
                });
            }
        }
    }
</script>
<style scoped>

</style>