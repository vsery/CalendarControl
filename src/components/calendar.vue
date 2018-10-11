<template>
    <div id="calendar-control">
        <div id="calendar-header">
            <div class="hello-msg">
                亲, <span ref="noon" title="">{{options.noon}}</span>好
            </div>
            <div class="current-time">
                <span ref="year" title="滚动鼠标滚轮调整年份">{{options.fullyear}}</span> 年
                <span ref="month" title="滚动鼠标滚轮调整月份">{{options.month}}</span> 月
            </div>
            <div class="sys-time-btn">
                <a href="JavasSript:;" class="prev">←</a>
                <a href="JavasSript:;" class="next">→</a>
                <a href="JavasSript:;" class="today">今天</a>
                <a href="javascript:location.reload();" class="refresh">刷新</a>
            </div>
            <ul class="calendar-format">
                <li :class="options.type == 'week'? 'active':''">列表</li>
                <li :class="options.type == 'month'? 'active':''">月历</li>
                <li :class="options.type == 'list'? 'active':''">周历</li>
            </ul>
        </div>
        <div id="calendar-content">
            <ul class="table-header">
                <template v-for="tabTitle, index in weeks">
                    <li class="work" v-if="index != 5 && index != 6"> <span>周{{tabTitle}}</span> </li>
                    <li class="rest" v-else> <span>周{{tabTitle}}</span> </li>
                </template>
            </ul>
            <ul class="table-content">
                <template v-for="item, index in dataList" v-if="dataList.length > 0">
                    <li>
                        <calendarView :year="item.year" :month="item.month" :days="item.days"></calendarView>
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
            options: {
                noon: '', // forenoon[上午], afternoon[下午]
                month: new Date().getMonth() + 1, // 获取当前月份(2位)
                year: new Date().getFullYear(), // 获取当前年份(4位)
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
        }
    },
    created() {
        this.options.noon = this.options.time.slice(0, 2);
    },
    mounted() {
        this.$nextTick(function() {
            this._initData();
        });
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
            // 计算本月有多少天；
            let days = new Date(year, month, 0).getDate();
            var _temp = []; // 零时数据
            // 构建当月数据
            for (var i = 1; i <= days; i++) {
                let _data = {
                    year: year,
                    month: month,
                    days: i
                };
                _temp.push(_data);
            }
            this.dataList = _temp; // 复制, 渲染
            console.log(this.dataList);
        },
    }
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import 'calendar.css'

</style>
