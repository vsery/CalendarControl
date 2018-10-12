<template>
    <div id="calendar-control">
        <div id="calendar-header">
            <div class="hello-msg">
                亲, <span ref="noon" title="">{{options.noon}}</span>好
            </div>
            <div class="current-time">
                <span ref="year" title="滚动鼠标滚轮调整年份">{{options.year}}</span> 年
                <span ref="month" title="滚动鼠标滚轮调整月份">{{options.month}}</span> 月
            </div>
            <div class="sys-time-btn">
                <a href="JavasSript:;" class="prev" @click="_setMonth('-')">←</a>
                <a href="JavasSript:;" class="next" @click="_setMonth('+')">→</a>
                <a href="JavasSript:;" class="today" @click="_setMonth">今天</a>
                <a href="JavasSript:location.reload();" class="refresh">刷新</a>
            </div>
            <ul class="calendar-format">
                <li @click="_setType('week')" :class="options.type == 'week'? 'active':''">周历</li>
                <li @click="_setType('month')" :class="options.type == 'month'? 'active':''">月历</li>
                <li @click="_setType('list')" :class="options.type == 'list'? 'active':''">列表</li>
            </ul>
        </div>
        <div id="calendar-content" :class="options.type">
            <ul class="table-header">
                <template v-for="tabTitle, index in weeks">
                    <li class="work" v-if="index != 5 && index != 6"> <span>周{{tabTitle}}</span> </li>
                    <li class="rest" v-else> <span>周{{tabTitle}}</span> </li>
                </template>
            </ul>
            <ul class="table-content">
                <template v-for="item, index in dataList" v-if="dataList.length > 0">
                    <li>
                        <!-- {{item.year}} {{item.month}} {{item.days}} {{item.isCurr}} -->
                        <calendarView ref="calendar" :year="item.year" :month="item.month" :days="item.days" :curr="item.isCurr"></calendarView>
                        <div class="data-content-box">
                            <template v-for="Citem, Cindex in weekLists" v-if="Citem.year == item.year && Citem.month == item.month && Citem.day == item.days">
                                <div class="data-content" @click="_openJourney(Citem)">
                                    <span class="content-name">{{Citem.name}}</span>
                                    <span class="content-body">{{Citem.content}}</span>
                                </div>
                            </template>
                        </div>
                    </li>
                </template>
            </ul>
        </div>
    </div>
</template>
<script>
import calendarDate from '@/components/calendarDate' // 时间过滤
export default {
    name: 'calendar',
    components: {
        'calendarView': calendarDate,
    },
    data() {
        return {
            journey: {
                isShow: false,
                name: null,
                content: null,
                year: null,
                month: null,
                day: null,
            },
            options: {
                noon: '', // forenoon[上午], afternoon[下午]
                year: new Date().getFullYear(), // 获取当前年份(4位)
                month: new Date().getMonth() + 1, // 获取当前月份(2位)
                date: new Date().getDate(), // 获取当前日(1-31)
                day: new Date().getDay(), // 获取当前星期X(0-6,0代表星期天)
                time: new Date().getTime(), // 获取当前时间(从1970.1.1开始的毫秒数)
                hours: new Date().getHours(), // 获取当前小时数(0-23)
                minutes: new Date().getMinutes(), // 获取当前分钟数(0-59)
                seconds: new Date().getSeconds(), // 获取当前秒数(0-59)
                milliseconds: new Date().getMilliseconds(), // 获取当前毫秒数(0-999)
                toLocaleDateString: new Date().toLocaleDateString(), // 获取当前日期
                time: new Date().toLocaleTimeString(), // 获取当前时间
                timeStr: new Date().toLocaleString(), // 获取日期与时间
                type: 'month', // week[周], month[月], list[列表]
                oneday: 'monday', // monday[一], sunday[日]
            },
            // 月份数组
            months: ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二'],
            // 日期数组
            days: ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二', '十三', '十四', '十五', '十六', '十七', '十八', '十九', '二十', '二十一', '二十二', '二十三', '二十四', '二十五', '二十六', '二十七', '二十八', '二十九', '三十', '三十一', '三十二'],
            // 星期数组
            weeks: ['一', '二', '三', '四', '五', '六', '日'],
            // 显示数据
            dataList: [],
            // 工作日志
            weekLists: [
                { year: 2018, month: 8, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 13, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 15, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 17, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 8, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                { year: 2018, month: 8, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                { year: 2018, month: 9, day: 21, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 21, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 13, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 13, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 9, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                { year: 2018, month: 9, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                { year: 2018, month: 10, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 10, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                { year: 2018, month: 10, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                { year: 2018, month: 11, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 15, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 15, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 11, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                { year: 2018, month: 11, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                { year: 2018, month: 12, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 2, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 4, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 6, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: 12, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                { year: 2018, month: 12, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },
            ],
        }
    },
    created() {
        this.options.noon = this.options.time.slice(0, 2);
    },
    mounted() {
        this._initData();
        // this.$nextTick(function() {});
    },
    methods: {
        /**
         * [_initData 构建数据]
         * @param  {[type]} year  [年]
         * @param  {[type]} month [月]
         * @param  {[type]} date  [日]
         * @return {[type]}       [渲染数据]
         */
        _initData: function(year, month, date) {
            if (year == null) { year = this.options.year; }
            if (month == null) { month = this.options.month; }
            if (date == null) { date = this.options.date; }
            // let showYear = year + '年';
            // let showMonth = this.months[month - 1] + '月';
            // let showDay = this.days[date - 1] + '日';
            // console.log(showYear, showMonth, showDay);
            let fristdays = new Date(year, month - 1, 0).getDate(); // 计算上月有多少天；
            let days = new Date(year, month, 0).getDate(); // 计算本月有多少天；
            let lastdays = new Date(year, month + 1, 0).getDate(); // 计算下月有多少天；
            let week = new Date(year, month - 1, 0).getDay(); // 本月第一天 周几
            let lastWeek = new Date(year, month - 1, days).getDay(); // 本月最后一天 周几
            console.log(lastWeek);
            var _temp = []; // 零时数据
            // 构建当月数据
            for (var i = 1; i <= days; i++) {
                let _data = {
                    year: year,
                    month: month,
                    days: i,
                    isCurr: true,
                };
                _temp.push(_data);
            }
            // 构建上月数据
            if (week != 1 && this.options.type == 'month') {
                for (var i = 0; i < week; i++) {
                    let _data = {
                        year: year,
                        month: month - 1,
                        days: fristdays - i,
                        isCurr: false,
                    };
                    _temp.unshift(_data);
                }

            }
            if (lastWeek != 0 && this.options.type == 'month') {
                for (var i = 1; i <= 7 - lastWeek; i++) {
                    let _data = {
                        year: year,
                        month: month + 1,
                        days: i,
                        isCurr: false,
                    };
                    _temp.push(_data);
                }
            }
            this.dataList = _temp; // 复制, 渲染
        },
        // 切换月份, 今天
        _setMonth: function(type) {
            // this.dataList = [];
            if (type == '-') {
                if (this.options.month == 1) {
                    this.options.year--;
                    this.options.month = 12;
                } else {
                    this.options.month--;
                }
            } else if (type == '+') {
                if (this.options.month == 12) {
                    this.options.year++;
                    this.options.month = 1;
                } else {
                    this.options.month++;
                }
            } else {
                this.options.year = new Date().getFullYear();
                this.options.month = new Date().getMonth() + 1;
                this.options.date = new Date().getDate();
            }
            // console.log(this.options.year + '-' + this.options.month);
            this._initData(this.options.year, this.options.month);
        },
        // 设置展示类型
        _setType: function(type) {
            this.options.type = type;
        },
        // 打开行程
        _openJourney(item) {
            var OBJ = item;
            OBJ.isShow = true;
            this.journey = OBJ;
        },
        // 关闭行程
        _closeJourney() {
            this.journey.isShow = false;
        }
    }
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import 'calendar.css'

</style>
