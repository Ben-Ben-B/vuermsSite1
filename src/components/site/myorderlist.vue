<template>
<div class="tmpl">
                <div class="sub-tit">
                    <ul>
                        <li class="selected">
                            <a href="/user/order-list.html">交易订单</a>
                        </li>
                    </ul>
                </div>
                <div class="table-wrap">
                    <table width="100%" border="0" cellspacing="0" cellpadding="0" class="ftable">
                        <tbody>
                            <tr>
                                <th width="16%" align="left">订单号</th>
                                <th width="10%">姓名</th>
                                <th width="12%">订单金额</th>
                                <th width="11%">下单时间</th>
                                <th width="10%">状态</th>
                                <th width="12%">操作</th>
                            </tr>
                            <tr v-for='item in orderlist' :key='item.id'>
                                <td v-text='item.order_no'></td>
                                <td align="left" v-text='item.accept_name'></td>
                                <td align="left">
                                    <strong style="color: red;">￥{{item.order_amount}}</strong>
                                    <br> 在线支付
                                </td>
                                <td align="left">{{item.add_time|datefmt('YYYY-MM-DD hh:mm:ss')}}</td>
                                <td align="left" v-text='item.statusName'>
                                    
                                </td>
                                <td align="left">
                                        <router-link v-bind="{to:'orderdetial/'+item.id}">查看订单</router-link>
                                    <br>
                                    <router-link v-if="item.status<=1" v-bind="{to:'/site/pay/'+item.id}">|去付款</router-link>
                                    <br>
                                    <a v-if="item.status<=1" href="javascript:void(0)" @click="cacelOrder(item.id)">|取消</a>
                                    <br>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    <div class="page-foot">
                        <div class="flickr right">
                                <el-pagination
                                @size-change="pageSizeChange"
                                @current-change="pageIndexChange"
                                :current-page="pageIndex"
                                :page-sizes="[10, 20, 30, 50,100,200]"
                                :page-size="pageSize"
                                layout="total, sizes, prev, pager, next, jumper"
                                :total="totalcount">
                              </el-pagination>
                        </div>
                    </div>
                </div>     
</div>
</template>

<script>
    export default {
        data() {
            return {
                pageIndex: 1,
                pageSize: 10,
                totalcount: 0,
                orderlist: []
            }
        },
        created() {
            this.getorderlist();
        },
        methods: {
            cacelOrder(id) {
                this.$http.get('/site/validate/order/cancelorder/' + id).then(res => {
                    if (res.data.status == 1) {
                        this.$message.error(res.data.message);
                    };
                    this.getorderlist();
                });
            },
            pageSizeChange(pageSize) {
                this.pageSize = pageSize;
                this.getorderlist();
            },
            pageIndexChange(pageIndex) {
                this.pageIndex = pageIndex;
                this.getorderlist();
            },
            getorderlist() {
                this.$http.get('/site/validate/order/userorderlist?pageIndex=' + this.pageIndex + '&pageSize=' + this.pageSize).then(res => {
                    if (res.data.status == 1) {
                        this.$message.error(res.data.message);
                    }
                    this.totalcount = res.data.totalcount;
                    this.orderlist = res.data.message;

                });
            }
        }
    }
</script>
<style scoped>

</style>