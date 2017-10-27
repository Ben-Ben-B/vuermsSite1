<template>
<div class="tmpl">
    <el-input-number v-model="count" @change="handleChange" :min="1"></el-input-number>
</div>
</template>

<script>
    export default {
        data() {
            return {
                count: 1
            }
        },
        // 用来接收父组件传入的值，格式：{gid:88,count:1}
        props: ['options'],
        created() {
            this.count = this.options.count;
        },
        methods: {
            handleChange(count) {
                this.count = count;
                this.send();
            },
            send() {
                this.$emit("update", {
                    gid: this.options.gid,
                    count: this.count
                });
            }
        }
    }
</script>
<style scoped>

</style>