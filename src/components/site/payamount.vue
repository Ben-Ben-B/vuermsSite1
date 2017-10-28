<template>
<div class="tmpl">
        <div class="tmpl">
                <input type="text" v-model="amount">
        
                <el-button type="success" @click="pay">支付</el-button>
            </div>
</div>
</template>

<script>
    export default {
        data() {
            return {
                amount: this.$route.params.amount
            }
        },
        methods: {
            pay() {
                var orderid = this.$route.params.orderid;
                this.$http.get('/site/validate/order/pay/' + orderid)
                    .then(res => {
                        // 跳转到支付成功的页面
                        this.$router.push({
                            name: 'paysuccess'
                        });
                    });
            }
        }
    }
</script>
<style scoped>

</style>