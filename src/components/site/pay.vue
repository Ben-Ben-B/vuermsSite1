<template>
    <div class="tmpl">
        <div class="section">
            <div class="location">
                <span>当前位置：</span>
                <a href="/index.html">首页</a> &gt;
                <a href="javascript:;">支付中心</a>
            </div>
        </div>

        <div class="section">
            <div class="wrapper">
                <div class="bg-wrap">
                    <div class="nav-tit pay">
                        <a href="javascript:;" class="selected">支付中心</a>
                    </div>
                    <div class="form-box payment">
                        <div class="el-row">
                            <div class="el-col el-col-16">
                                <div class="el-row">
                                    <div class="el-col el-col-12">
                                        <dl class="form-group">
                                            <dt>订 单 号：</dt>
                                            <dd>{{orderinfo.order_no}}</dd>
                                        </dl>
                                    </div>
                                    <div class="el-col el-col-12">
                                        <dl class="form-group">
                                            <dt>收货人姓名：</dt>
                                            <dd v-text='orderinfo.accept_name'></dd>
                                        </dl>
                                    </div>
                                </div>
                                <div class="el-row">
                                    <div class="el-col el-col-12">
                                        <dl class="form-group">
                                            <dt>送货地址：</dt>
                                            <dd v-text='orderinfo.area'>
                                            </dd>
                                        </dl>
                                    </div>
                                    <div class="el-col el-col-12">
                                        <dl class="form-group">
                                            <dt>手机号码：</dt>
                                            <dd v-text='orderinfo.mobile'></dd>
                                        </dl>
                                    </div>
                                </div>
                                <div class="el-row">
                                    <div class="el-col el-col-12">
                                        <dl class="form-group">
                                            <dt>支付金额：</dt>
                                            <dd>{{orderinfo.order_amount}} 元</dd>
                                        </dl>
                                    </div>
                                    <div class="el-col el-col-12">
                                        <dl class="form-group">
                                            <dt>支付方式：</dt>
                                            <dd>在线支付</dd>
                                        </dl>
                                    </div>
                                </div>
                                <div class="message">
                                    <span>备&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;注：</span>
                                    <span v-text='orderinfo.message'>sdf</span>
                                </div>
                            </div>
                            <div class="el-col el-col-8">
                                <div id="container">
                                    二维码图片
                                    <canvas width="300" height="300"></canvas>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
    </div>
</template>

<script>
    import '../../../statics/site/js/jqplugins/qr/jqueryqr'

    import kits from '../../kits/kits.js'
    export default {
        data() {
            return {
                orderinfo: {},
                orderid: this.$route.params.orderid,
                interval: null
            }
        },
        created() {
            this.getorderinfo();
        },
        mounted() {
            var amount = this.$route.params.amount;

            // 设置二维码
            $("#container").erweima({
                label: '黑马广州前端支付',
                text: kits.payurl + '/' + this.orderid + '/' + amount
            });
            this.interval = setInterval(this.checkorderStatus, 5000);

        },
        beforeDestroy() {
            if (this.interval) {
                // 清空
                clearInterval(this.interval);
            }
        },
        methods: {
            checkorderStatus() {
                this.$http.get('/site/validate/order/getorder/' + this.orderid).then(res => {
                    // 返回数据中的status为2的时候就跳转到支付成功页面，否则一直停留在支付页面
                    if (res.data.message[0].status == 2) {
                        // 支付成功跳转到
                        this.$router.push({
                            name: 'paysuccesspc'
                        });
                    }
                });
            },
            getorderinfo() {
                this.$http.get('/site/validate/order/getorder/' + this.orderid).then(res => {
                    if (res.data.status == 1) {
                        this.$message.error(res.data.message);
                    };
                    this.orderinfo = res.data.message[0];
                    console.log(this.orderinfo)
                });
            }
        }
    }
</script>
<style>
    .form-box .form-group dt {
        width: 30% !important;
    }
</style>