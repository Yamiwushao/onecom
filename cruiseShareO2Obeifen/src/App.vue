<template>
    <div id="cruise-share">
        <div class="swiper-container swiper-container-v">
            <div class="swiper-wrapper swiper-wrapper-v">
                <div class="swiper-slide swiper-slide-v">
                    <div class="content content-product">
                        <div class="swiper-container product-container-h">
                            <div class="swiper-wrapper product-wrap-h">
                                <template v-if="pkgImageList && pkgImageList.length>0">
                                    <div class="swiper-slide product-slide-h" v-for="image in pkgImageList">
                                        <img class="pkgImg" :src="image">
                                    </div>
                                </template>
                            </div>
                            <div class="swiper-pagination product-pagination-h"></div>
                        </div>
                        <p class="product-name" v-if="pkgBaseInfo && pkgBaseInfo.productName">{{pkgBaseInfo.productName}}</p>
                        <p class="product-price" v-if="lowestSalePriceYuan">¥ <span>{{lowestSalePriceYuan}}</span> 起/人
                        </p>
                        <div class="arrow" @click="slideToV(0)"></div>
                    </div>
                </div>
                <div class="swiper-slide swiper-slide-v">
                    <div class="content content-detail">
                        <div class="tab-wrap">
                            <ul class="tab">
                                <li class="tab-item" :class="{'tab-active': detailTabState[0]}"
                                    @click="detailTabChange(0)" v-if="cabinBranch && cabinBranch.length>0">
                                    <a>住宿</a></li>
                                <li class="tab-item" :class="{'tab-active': detailTabState[1]}"
                                    @click="detailTabChange(1)" v-if="restaurantBranch && restaurantBranch.length>0">
                                    <a>餐饮</a></li>
                                <li class="tab-item" :class="{'tab-active': detailTabState[2]}"
                                    @click="detailTabChange(2)" v-if="entertainBranch &&  entertainBranch.length>0">
                                    <a>娱乐</a></li>
                                <li class="tab-item" :class="{'tab-active': detailTabState[3]}"
                                    @click="detailTabChange(3)" v-if="shoppingBranch && shoppingBranch.length>0">
                                    <a>购物</a></li>
                            </ul>
                        </div>
                        <div class=""></div>
                    </div>
                </div>
                <div class="swiper-slide swiper-slide-v " :class="{'swiper-no-swiping':noSwiping}">
                    <div class="content content-route">
                        <div class="swiper-container tab-contain">
                            <div class="swiper-wrapper"
                                 v-if="pkgLineRoute && pkgLineRoute.lineRouteDetails && pkgLineRoute.lineRouteDetails.length>0">
                                <div class="swiper-slide tab-item" :class="{'tab-active': routeTabState[0] }" @click="routeTabChange(0)">
                                    <a ref="day">行程</a>
                                </div>
                                <div class="swiper-slide tab-item" :class="{'tab-active': routeTabState[$index] }" v-for="$index in pkgLineRoute.lineRouteDetails.length" @click="routeTabChange($index)">
                                    <a ref="day">第{{$index | numberToChinese }}天</a>
                                </div>
                            </div>
                        </div>
                        <div class="swiper-container route-container-h">
                            <div class="swiper-wrapper route-wrap-h">
                                <div class="swiper-slide route-slide-h">
                                    <div class="route-img">
                                        <img src="">
                                    </div>
                                    <div class="route-info">
                                        <p class="total-day">6天5晚</p>
                                        <p class="start-end">2017/4/5 周三出发，周一返回</p>
                                    </div>
                                </div>
                                <div class="swiper-slide route-slide-h day-route"
                                     v-for="item in pkgLineRoute.lineRouteDetails">
                                    <h1 class="route-title">{{item.title}}</h1>
                                    <p class="meals-desc-CN">{{item.mealsDescCN}}</p>
                                    <div class="route-descs">
                                        <template v-for="desc in item.lineRouteDescs">
                                            <h3 class="title">{{desc.title}}</h3>
                                            <p class="descs-content">
                                                {{desc.content}}
                                            </p>
                                        </template>
                                    </div>
                                    <span class="more" @click="more(item.lineRouteDescs)">查看更多</span>
                                </div>
                            </div>
                        </div>
                        <day-route-model v-if="modelState" :descs="descs" @close="close()"></day-route-model>
                        <div class="arrow" @click="slideToV(2)"></div>
                    </div>
                </div>
                <div class="swiper-slide swiper-slide-v">
                    <div class="content content-order">
                        <div class="arrow-back"></div>
                        <div class="product-info" v-show="productInfoState">
                            <p class="product-name">［皇家加勒比 海洋量子号］上海－福冈－熊本－上海 6天5晚 4月5日</p>
                            <p class="product-price">¥ <span>2800</span> 起/人</p>
                        </div>
                        <div class="counsel-order">
                            <h3 class="title">咨询或预定</h3>
                            <input class="phone-num" placeholder="请输入您的手机号码" @focus="onFocus()" @blur="outFocus()"
                                   :class="{active:!productInfoState}">
                            <p class="message">让驴妈妈来联系您</p>
                        </div>
                        <p class="counsel-tele">咨询电话 159 2192 5160<span class="right-arrow"></span></p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import 'swiper'
    import 'swiper/dist/css/swiper.css'
    import DayRouteModel from './components/DayRouteModel'

    export default {
        components: {
            'day-route-model': DayRouteModel
        },
        data () {
            return {
                // 邮轮组合产品
                pkgBaseInfo: {},
                pkgGroupDateList: [],
                pkgLineRoute: {},
                pkgLineShipDetailList: [],
                pkgReserveClause: {},
                pkgImageList: [],
                lowestSalePriceYuan: 0,

                // 邮轮产品
                cruiseBaseInfo: {},
                cabinBranch: [],
                entertainBranch: [],
                restaurantBranch: [],
                shoppingBranch: [],

                //第二屏tab切换
                detailTabState: [true, false, false, false],

                //第三屏tab状态
                routeTabState: [true],
                modelState: false,
                descs: [],
                noSwiping: false,
                productInfoState: true,
                deviValue: 0,
                pagiWrapWidth: 0,
                bulletNumList: [0],
                bulletWidth: 18,
                baseNavTranslate: 94,
                tabTranslate: 0,
            }
        },
        created () {
            //初始化小圆点可见宽度
            this.pagiWrapWidth = this.cabinBranch.length * this.bulletWidth;

            //获取数据
            this.getPackagedCruiseDetail();
            this.getCruiseDetail();


            //初始化 每个品类对应的小圆点的个数
            this.bulletNumList.push(this.cabinBranch.length);
            this.bulletNumList.push(this.cabinBranch.length + this.restaurantBranch.length);
            this.bulletNumList.push(this.cabinBranch.length + this.restaurantBranch.length + this.entertainBranch.length);
            this.bulletNumList.push(this.cabinBranch.length + this.restaurantBranch.length + this.entertainBranch.length + this.shoppingBranch.length);
        },
        mounted () {
            var _this = this;

            this.swiperV = new Swiper('.swiper-container-v', {
                direction: 'vertical',
                spaceBetween: 50,
                noSwiping: true
            });
        },
        updated () {
            const _this = this;

            //垂直轮播
            this.prodSwiperH = new Swiper('.product-container-h', {
                pagination: '.product-pagination-h',
                paginationClickable: true,
            });

            // 第二屏轮播
            this.detailSwiperH = new Swiper('.detail-container-h', {
                pagination: '.detail-pagination-h',
                paginationClickable: true,
                uniqueNavElements: false,
                onSlideChangeStart: function (swiper) {
                    console.log(swiper.activeIndex);
                    // let activeIndex = swiper.activeIndex;
                    // if (activeIndex >= _this.bulletNumList[0] && activeIndex < _this.bulletNumList[1]) {
                    //     _this.detailSwitch(0,_this.cabinBranch.length)
                    // } else if (activeIndex < _this.bulletNumList[2]) {
                    //     _this.detailSwitch(1,_this.restaurantBranch.length)
                    // } else if (activeIndex < _this.bulletNumList[2]) {
                    //     _this.detailSwitch(2,_this.entertainBranch.length)
                    // } else {
                    //     _this.detailSwitch(3,_this.shoppingBranch.length)
                    // }
                }
            });

            // 第三屏轮播 tab 轮播
            this.tabSwiper = new Swiper('.tab-contain', {
                slidesPerView: 4,
            });

            // 第三屏 路线 轮播
            this.routeSwiperH = new Swiper('.route-container-h', {
                onSlideChangeStart: function (swiper) {
                    if (swiper.activeIndex >= 4) {
                        _this.tabSwiper.slideTo(swiper.activeIndex,1000,false);
                    }else{
                        _this.tabSwiper.slideTo(0);
                        _this.tabTranslate = 0;
                    }
                    _this.routeTabState.forEach((value, index)=> {
                        _this.routeTabState.splice(index, 1, false);
                    });
                    _this.routeTabState.splice(swiper.activeIndex, 1, true);
                }
            });

        },
        methods: {
            getPackagedCruiseDetail () {
                const url = '../../static/pkgdetail.json';
                this.$http.get(url).then((res)=> {
                    console.log(res);
                    res = res.body;
                    if (res.code === '1') {
                        this.pkgBaseInfo = res.data.pkgCruiseBaseInfo;
                        this.pkgGroupDateList = res.data.pkgCruiseGroupDateList;
                        this.pkgLineRoute = res.data.pkgCruiseLineRoute;
                        this.pkgImageList = res.data.pkgCruiseImageList;
                        this.pkgLineShipDetailList = res.data.pkgCruiseLineShipDetailList;
                        this.pkgReserveClause = res.data.pkgCruiseReserveClause;
                        this.visaType = res.data.visaType;
                        this.signature = res.signature;
                        this.commissionAmountFen = res.data.commissionAmountFen;
                        this.commissionAmountYuan = res.data.commissionAmountYuan;
                        this.lowestSalePriceFen = res.data.lowestSalePriceFen;
                        this.lowestSalePriceYuan = res.data.lowestSalePriceYuan;
                    }
                }, (req)=> {

                })
            },
            getCruiseDetail() {
                const url = '../../static/detail.json';
                this.$http.get(url).then((res)=> {
                    res = res.body;
                    if (res.code === '1') {
                        this.cruiseBaseInfo = res.data.cruiseBaseInfo;
                        this.cabinBranch = res.data.cabinProductBranch;
                        this.entertainBranch = res.data.entertainmentProductBranch;
                        this.restaurantBranch = res.data.restaurantProductBranch;
                        this.shoppingBranch = res.data.shoppingProductBranch;
                    }
                }, (req)=> {

                })
            },
            slideToV (activeIndex) {
                if (activeIndex < 3) {
                    this.swiperV.slideTo(activeIndex + 1, 1000, false);
                } else {
                    this.swiperV.slideTo(activeIndex - 1, 1000, false);
                }
            },
            routeTabChange (index) {
                console.log(index);
                this.routeSwiperH.slideTo(index);
            },
            more (descs) {
                this.modelState = true;
                this.noSwiping = true;
                this.descs = descs;
            },
            close () {
                this.modelState = false;
                this.noSwiping = false;
            },
            onFocus () {
                this.productInfoState = false;
            },
            outFocus () {
                this.productInfoState = true;
            },
            detailTabChange (value) {
                var _this = this;
                value = Number(value);
                var bulletNumList = _this.bulletNumList;
                switch (value) {
                    case 0: {
                        if (_this.cabinBranch.length === 0) return;
                        _this.detailSwitch(0, _this.cabinBranch.length)
                        _this.detailSwiperH.slideTo(0, 1000, false)
                        break;
                    }
                    case 1: {
                        if (_this.restaurantBranch.length === 0) return;
                        _this.detailSwitch(1, _this.restaurantBranch.length)
                        break;
                    }
                    case 2: {
                        if (_this.entertainBranch.length === 0) return;
                        _this.detailSwitch(2, _this.entertainBranch.length)
                        break;
                    }
                    case 3: {
                        _this.detailTabState.splice(3, 1, true);
                        _this.detailSwitch(3, _this.entertainBranch.length)
                        break;
                    }
                    default:
                }
                ;

            },
            detailSwitch (activeIndex, bulletsCount) {
                const _this = this;

//                每个小圆点的宽度
                const bulletWidth = _this.bulletWidth;

//                去掉顶部导航的样式
                _this.detailTabState.forEach((value, index)=> {
                    _this.detailTabState.splice(index, 1, false);
                });

//                为当前导航item添加样式
                _this.detailTabState.splice(activeIndex, 1, true);

//              修改小圆点导航栏的可见宽度
                _this.pagiWrapWidth = bulletsCount * bulletWidth;

//                将当前显示的图片对应的小圆点移动到可见区域
                _this.deviValue = _this.bulletNumList[activeIndex] * bulletWidth;
            },
        },
        filters: {
            numberToChinese (num) {
                var chnNumChar = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九'];
                var chnUnitChar = ['', '十', '百', '千'];
                var strIns = '', chnStr = '';
                var unitPos = 0;
                var zero = true;
                while (num > 0) {
                    var v = num % 10;
                    if (v === 0) {
                        if (!zero) {
                            zero = true;
                            chnStr = chnNumChar[v] + chnStr;
                        }
                    } else {
                        zero = false;
                        strIns = chnNumChar[v];
                        strIns += chnUnitChar[unitPos];
                        chnStr = strIns + chnStr;
                    }
                    unitPos++;
                    num = Math.floor(num / 10);
                }
                return chnStr;
            }
        }

    }
</script>

<style rel="stylesheet/scss" lang="scss">
    html, body {
        width: 100%;
        height: 100%;
        margin: 0;
        font-family: microsoft yahei Arial;
    }

    ul {
        margin: 0;
        padding: 0;
    }

    li {
        list-style: none;
    }

    #cruise-share {
        width: 100%;
        height: 100%;
        .swiper-container-v {
            width: 100%;
            height: 100%;
            .swiper-wrapper-v {
                width: 100%;
                height: 100%;
                .swiper-slide-v {
                    width: 100%;
                    height: 100%;
                    -webkit-box-sizing: border-box;
                    -moz-box-sizing: border-box;
                    box-sizing: border-box;
                    padding-top: 17.07%;
                    .content {
                        position: relative;
                        width: 100%;
                        height: 100%;
                        -webkit-box-sizing: border-box;
                        -moz-box-sizing: border-box;
                        box-sizing: border-box;
                        background: url("assets/images/bgimg.jpg") no-repeat center;
                        -webkit-background-size: 100% 100%;
                        background-size: 100% 100%;
                    }
                    @keyframes arrowMove {
                        0% {
                            -webkit-transform: translateX(-50%) translateY(0);
                            -moz-transform: translateX(-50%) translateY(0);
                            -ms-transform: translateX(-50%) translateY(0);
                            -o-transform: translateX(-50%) translateY(0);
                            transform: translateX(-50%) translateY(0);
                        }
                        100% {
                            -webkit-transform: translateX(-50%) translateY(15px);
                            -moz-transform: translateX(-50%) translateY(15px);
                            -ms-transform: translateX(-50%) translateY(15px);
                            -o-transform: translateX(-50%) translateY(15px);
                            transform: translateX(-50%) translateY(15px);
                        }
                    }
                    .arrow {
                        position: absolute;
                        bottom: 4.5%;
                        left: 50%;
                        -webkit-transform: translateX(-50%);
                        -moz-transform: translateX(-50%);
                        -ms-transform: translateX(-50%);
                        -o-transform: translateX(-50%);
                        transform: translateX(-50%);
                        -webkit-animation: arrowMove 2s linear infinite;
                        -o-animation: arrowMove 2s linear infinite;
                        animation: arrowMove 2s linear infinite;
                        width: 48px;
                        height: 19px;
                        background: url("assets/images/arrow.png");
                        -webkit-background-size: cover;
                        background-size: cover;
                    }

                }
            }
        }

        /*第一屏*/
        .content-product {
            padding-top: 35.5%;
            .product-container-h {
                width: 84%;
                height: 52%;
                .product-wrap-h {
                    width: 100%;
                    height: 86.3%;
                    .product-slide-h {
                        background: #ffffff url("assets/images/lvmama.png") no-repeat center;
                        -webkit-background-size: cover;
                        background-size: cover;
                        -webkit-border-radius: 2px;
                        -moz-border-radius: 2px;
                        border-radius: 2px;
                        .pkgImg {
                            width: 100%;
                            height: 100%;
                        }
                    }
                }
                .product-pagination-h {
                    .swiper-pagination-bullet {
                        background-color: #ffffff;
                        opacity: 0.3;
                    }
                    .swiper-pagination-bullet-active {
                        opacity: 1;
                    }
                }
            }
            .product-name {
                width: 84%;
                margin: 6.7% auto 8%;
                font-size: 18px;
                color: #fff;
            }
            .product-price {
                text-align: center;
                margin: 0 auto;
                font-size: 16px;
                color: #fff;
                span {
                    font-size: 28px;
                }

            }
        }

        /*第二屏*/
        .content-detail {
            padding-top: 11.6%;
            .tab-wrap {
                height: 9.6%;
                width: 84%;
                margin: 0 auto;
                overflow: hidden;
                .tab {
                    display: flex;
                    height: 100%;
                    width: 100%;
                    .tab-item {
                        flex: 1;
                        height: 100%;
                        float: left;
                        line-height: 100%;
                        text-align: center;
                        font-size: 16px;
                        opacity: 0.6;
                        a {
                            display: block;
                            width: 100%;
                            height: 100%;
                            line-height: 53.7px;
                            color: #fff;
                        }
                    }
                    .tab-item:nth-child(1) {
                        width: 7.1%;
                    }
                    .tab-active {
                        font-size: 18px;
                        font-weight: 700;
                        opacity: 1;
                    }
                }
            }
            .detail-container-h {
                width: 84%;
                height: 100%;
                float: left;
                margin-left: 30px;
                .detail-wrap-h {
                    width: 100%;
                    height: 92.14%;
                    .detail-slide-h {
                        width: 100%;
                        height: 100%;
                        background-color: #ffffff;
                        .detail-img {
                            width: 100%;
                            height: 56%;
                            background: url("assets/images/lvmama.png") no-repeat center;
                            background-color: skyblue;
                            -webkit-background-size: cover;
                            background-size: cover;
                        }
                        .branch-info {
                            margin-top: 6.98%;
                            .branch-name {
                                font-size: 18px;
                                color: #333;
                            }
                        }
                    }
                }
            }
            .pagination-wrap {
                position: absolute;
                bottom: 10px;
                left: 50%;
                -webkit-transform: translateX(-50%);
                -moz-transform: translateX(-50%);
                -ms-transform: translateX(-50%);
                -o-transform: translateX(-50%);
                transform: translateX(-50%);
                z-index: 10;
                width: 72px;
                height: 21px;
                overflow: hidden;
                .detail-pagination-h {
                    width: 1000%;
                    text-align: left;
                }
                .swiper-pagination-bullet {
                    margin: 0 5px;
                }
            }
        }

        /*第三屏*/
        .content-route {
            position: relative;
            padding-top: 11.6%;
            text-align: left;
            .tab-contain {
                height: 9.6%;
                width: 92%;
                margin-left: 8%;
                overflow: hidden;
                text-align: center;
                .tab-item {
                    font-size: 16px;
                    color: #fff;
                    a {
                        display: flex;
                        justify-content:center;
                        align-items:center;
                        width: 100%;
                        height: 100%;
                    }
                }
                .tab-active {
                    font-size: 18px;
                    font-weight: 700;
                }

            }
            .route-container-h {
                width: 84%;
                height: 72.75%;
                float: left;
                margin-left: 30px;
                .route-wrap-h {
                    width: 100%;
                    height: 92.14%;
                    .route-slide-h {
                        width: 100%;
                        height: 100%;
                        background-color: #ffffff;
                        -webkit-border-radius: 2px;
                        -moz-border-radius: 2px;
                        border-radius: 2px;
                        .route-img {
                            width: 100%;
                            height: 56%;
                            background: url("assets/images/lvmama.png") no-repeat center;
                            background-color: skyblue;
                            -webkit-background-size: cover;
                            background-size: cover;
                            border-top-left-radius: 2px;
                            border-top-right-radius: 2px;
                        }
                        .route-info {
                            margin-top: 7.9%;
                            p {
                                text-align: left;
                                padding-left: 6.35%;
                            }
                            .total-day {
                                font-size: 18px;
                                color: rgb(51, 51, 51);
                                font-weight: 700;
                            }
                            .start-end {
                                margin-top: 6.35%;
                                font-size: 16px;
                                color: rgb(102, 102, 102);
                            }
                        }
                    }
                }
                .route-pagination-h {
                    position: relative;
                    top: 0;
                    bottom: auto;
                }
            }
            .day-route {
                -webkit-box-sizing: border-box;
                -moz-box-sizing: border-box;
                box-sizing: border-box;
                padding: 6.34% 3.2% 0;
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
                    height: 64%;
                    overflow: hidden;
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
                .more {
                    position: absolute;
                    bottom: 5.3%;
                    left: 50%;
                    -webkit-transform: translateX(-50%);
                    -moz-transform: translateX(-50%);
                    -ms-transform: translateX(-50%);
                    -o-transform: translateX(-50%);
                    transform: translateX(-50%);
                    font-size: 14px;
                    color: rgb(34, 153, 238)
                }
            }
        }

        /*第四屏*/
        .content-order {
            padding-top: 24%;
            @keyframes arrowBackMove {
                0% {
                    -webkit-transform: translateX(-50%) translateY(15px) rotateX(180deg);
                    -moz-transform: translateX(-50%) translateY(15px) rotateX(180deg);
                    -ms-transform: translateX(-50%) translateY(15px) rotateX(180deg);
                    -o-transform: translateX(-50%) translateY(15px) rotateX(180deg);
                    transform: translateX(-50%) translateY(15px) rotateX(180deg);
                }
                100% {
                    -webkit-transform: translateX(-50%) translateY(0) rotateX(180deg);
                    -moz-transform: translateX(-50%) translateY(0) rotateX(180deg);
                    -ms-transform: translateX(-50%) translateY(0) rotateX(180deg);
                    -o-transform: translateX(-50%) translateY(0) rotateX(180deg);
                    transform: translateX(-50%) translateY(0) rotateX(180deg);
                }

            }
            .arrow-back {
                position: absolute;
                top: 4.9%;
                left: 50%;
                -webkit-transform: translateX(-50%) rotateX(180deg);
                -moz-transform: translateX(-50%) rotateX(180deg);
                -ms-transform: translateX(-50%) rotateX(180deg);
                -o-transform: translateX(-50%) rotateX(180deg);
                transform: translateX(-50%) rotateX(180deg);
                -webkit-animation: arrowBackMove 2s linear infinite;
                -o-animation: arrowBackMove 2s linear infinite;
                animation: arrowBackMove 2s linear infinite;
                width: 48px;
                height: 19px;
                background: url("assets/images/arrow.png");
                -webkit-background-size: cover;
                background-size: cover;
            }
            .product-info {
                width: 100%;
            }
            .product-name {
                width: 84%;
                margin: 0 auto 8%;
                font-size: 18px;
                color: #fff;
            }
            .product-price {
                text-align: center;
                margin: 0 auto;
                font-size: 16px;
                color: #fff;
                span {
                    font-size: 28px;
                }

            }
            .counsel-order {
                width: 84%;
                height: 37.1%;
                margin: 13.34% auto 0;
                background-color: #ffffff;
                -webkit-border-radius: 2px;
                -moz-border-radius: 2px;
                border-radius: 2px;;
                .title {
                    margin: 0;
                    padding-top: 11%;
                    text-align: center;
                    font-size: 18px;
                    line-height: 18px;
                    font-weight: 600;
                }
                input::-webkit-input-placeholder {
                    color: #ccc;
                    font-size: 16px;
                    text-align: center;
                }
                input:-moz-placeholder {
                    color: #ccc;
                    font-size: 16px;
                    text-align: center;
                }
                input::-moz-placeholder {
                    color: #ccc;
                    font-size: 16px;
                    text-align: center;
                }
                input:-ms-input-placeholder {
                    color: #ccc;
                    font-size: 16px;
                    text-align: center;
                }
                .phone-num {
                    display: block;
                    width: 83.8%;
                    height: 18.8%;
                    margin: 11% auto 0;
                    padding: 0;
                    text-align: center;
                    border: 1px solid #bbbbbb;
                    outline: none;
                }
                .message {
                    display: flex;
                    align-items: center;
                    justify-content: center;
                    height: 26.57%;
                    margin: 7.9% 0 0;
                    background-color: #ff8800;
                    font-size: 18px;
                    line-height: 18px;
                    color: #fff;
                }
            }
            .counsel-tele {
                margin-top: 9.5%;
                text-align: center;
                font-size: 16px;
                color: #fff;
                .right-arrow {
                    display: inline-block;
                    height: 10px;
                    width: 10px;
                    margin-left: 10px;
                    border-right: 2px solid #fff;
                    border-top: 2px solid #fff;
                    transform: rotate(45deg);
                    -webkit-transform: rotate(45deg);
                }
            }
        }
    }


</style>
