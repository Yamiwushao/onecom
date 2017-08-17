<template>
    <div class="day-route-model">
        <span class="close" @click="close()"></span>
        <h1 class="route-title">海上巡游</h1>
        <p class="meals-desc-CN">早餐含/午餐含/晚餐自理</p>
        <div class="route-descs" v-for="item in descs">
            <h3 class="title">{{item.title}}</h3>
            <p class="descs-content">{{item.content}}</p>
        </div>
    </div>
</template>
<script type="text/javascript">
    export default {
        props: ["descs"],
        name: '',
        data () {
            return {
            }
        },
        methods: {
            close () {
                this.$emit('close');
            },
        }
    }
</script>
<style rel="stylesheet/scss" scoped lang="scss">
    .day-route-model {
        position: absolute;
        top: 1.66%;
        bottom: 1.66%;
        left: 2.67%;
        right: 2.67%;
        z-index: 1000;
        padding: 5.34% 2.67%;
        background-color: #ffffff;
        -webkit-border-radius: 2px;
        -moz-border-radius: 2px;
        border-radius: 2px;
        overflow-y: scroll;
        .close {
            position: absolute;
            top: 5%;
            right: 5.64%;
            width: 23px;
            height: 3px;
            background-color: #aaaaaa;
            line-height: 0;
            font-size: 0;
            vertical-align: middle;
            -webkit-transform: rotate(45deg);
        }
        .close:after {
            content: '/';
            display: block;
            width: 23px;
            height: 3px;
            background: #aaaaaa;
            -webkit-transform: rotate(-90deg);
        }
        .route-title {
            margin: 0;
            text-align: center;
            font-size: 18px;
            font-weight: 600;
            color: rgb(51, 51, 51);
        }
        .meals-desc-CN {
            margin-top: 11.8%;
            font-size: 16px;
            color: rgb(102, 102, 102);
        }
        .route-descs {
            position: relative;
            .title {
                margin: 8.5% 0 0;
                font-size: 16px;
                font-weight: 700;
                color: rgb(51, 51, 51);
            }
            .descs-content {
                margin-top: 3.4%;
                font-size: 16px;
                color: rgb(102, 102, 102);
            }

        }
    }
</style>