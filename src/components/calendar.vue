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
                <template v-for="tabTitle, index in days">
                    <li class="work" v-if="index != 5 && index != 6"> <span>周{{tabTitle}}</span> </li>
                    <li class="rest" v-else> <span>周{{tabTitle}}</span> </li>
                </template>
            </ul>
            <ul class="table-content">
                <template v-for="tab, index in dataList" v-if="dataList.length > 0">
                    <li>
                        <dl>
                            <dt>{{tab}}</dt>
                            <dd></dd>
                        </dl>
                    </li>
                </template>
            </ul>
        </div>
    </div>
</template>
<script>
export default {
    name: 'calendar',
    data() {
        return {
            newDate: new Date(),
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
            currentTime: null, // 当前时间
            fullDate: {},
            userData: {},
            months: ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二'],
            days: ['一', '二', '三', '四', '五', '六', '日'],
            nums: ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二', '十三', '十四', '十五', '十六', '十七', '十八', '十九', '二十', '二十一', '二十二', '二十三', '二十四', '二十五', '二十六', '二十七', '二十八', '二十九', '三十', '三十一', '三十二'],
            solar: ['0105小寒', '0120大寒', '0203立春', '0218雨水', '0305惊蜇', '0320春分', '0404清明', '0419谷雨', '0505立夏', '0520小满', '0605芒种', '0621夏至', '0706小暑', '0722大暑', '0807立秋', '0822处暑', '0907白露', '0922秋分', '1008寒露', '1023霜降', '1107立冬', '1122小雪', '1206大雪', '1221冬至'],
            dataList: [],
        }
    },
    watch: {},
    props: {},
    beforeCreate() {},
    created() {
        this.options.noon = this.options.time.slice(0, 2);
    },
    beforeMount() {},
    mounted() {
        console.log(this.options, new Date().toSource);
        this.$nextTick(function() {
            this.initData(2000, 10);
        });
    },
    beforeUpdate() {
        // console.group('更新前状态  ===============》beforeUpdate');
    },
    updated() {
        // console.group('更新完成状态===============》updated');
    },
    beforeDestroy() {
        // console.group('销毁前状态  ===============》beforeDestroy');
    },
    destroyed() {
        // console.group('销毁完成状态===============》destroyed');
    },
    methods: {
        initData: function(year, month, date) {
            if (year == null) { year = this.options.year; }
            if (month == null) { month = this.options.month; }
            if (date == null) { date = this.options.date; }
            // console.log(year, month, date);
            let showYear = year + '年';
            let showMonth = this.months[month - 1] + '月';

            // 计算本月1号是周几；
            let week = new Date(year + '-' + month + '-1').getDay();

            // 计算本月有多少天；
            let days = new Date(year, month, 0).getDate();

            // 计算上月有多少天；
            let dayw = new Date(year, month - 1, 0).getDate();

            // console.log(week, days, dayw);
            console.log(showYear, showMonth);

            //将日历填回页面；拿出节假日
            for (let i = 1; i <= days; i++) {
                let _data = {
                    day: null, // 阳历
                    week: null, // 周几
                    lunar: null, // 农历
                    fest: null, // 节日
                    solar: null, // 节气
                    work: []
                };
                _data.day = i;
                let week = new Date(year, month - 1, i).getDay();
                switch (week) {
                    case 1:
                        _data.week = '周一';
                        break;
                    case 2:
                        _data.week = '周二';
                        break;
                    case 3:
                        _data.week = '周三';
                        break;
                    case 4:
                        _data.week = '周四';
                        break;
                    case 5:
                        _data.week = '周五';
                        break;
                    case 6:
                        _data.week = '周六';
                        break;
                    case 0:
                        _data.week = '周日';
                        break;
                }
                switch (parseInt(month)) {
                    case 1:
                        if (i == 10) { _data.fest = '教师节'; }
                        break;
                    case 2:
                        if (i == 10) { _data.fest = '教师节'; }
                        break;
                    case 3:
                        if (i == 8) { _data.fest = '妇女节'; }
                        if (i == 12) { _data.fest = '植树节'; }
                        break;
                    case 4:
                        if (i == 5) { _data.fest = '清明节'; }
                        break;
                    case 5:
                        if (i == 1) { _data.fest = '劳动节'; }
                        break;
                    case 6:
                        if (i == 1) { _data.fest = '儿童节'; }
                        break;
                    case 7:
                        if (i == 1) { _data.fest = '中国共产党诞生纪念日'; }
                        // if (i == 7) { _data.fest = '七夕节'; }
                        break;
                    case 8:
                        if (i == 1) { _data.fest = '建军节'; }
                        break;
                    case 9:
                        // if (i == 10) { _data.fest = '教师节'; }
                        break;
                    case 10:
                        if (i == 1) { _data.fest = '国庆节'; }
                        if (i == 17) { _data.fest = '重阳节'; }
                        break;
                    case 11:
                        if (i == 11) { _data.fest = '光棍节'; }
                        break;
                    case 12:
                        // if (i == 10) { _data.fest = '教师节'; }
                        break;
                }
                this.dataList.push(_data);
                // var time = new Date(year, month, i).getTime();
                // if (month + '-' + i == '1-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>元旦</i></li>"
                // } else if (month + '-' + i == '2-14') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>情人节</i></li>"
                // } else if (month + '-' + i == '3-8') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>妇女节</i></li>"
                // } else if (month + '-' + i == '4-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>愚人节</i></li>"
                // } else if (month + '-' + i == '5-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>劳动节</i></li>"
                // } else if (month + '-' + i == '6-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>儿童节</i></li>"
                // } else if (month + '-' + i == '7-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>建党节</i></li>"
                // } else if (month + '-' + i == '8-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>建军节</i></li>"
                // } else if (month + '-' + i == '9-10') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>教师节</i></li>"
                // } else if (month + '-' + i == '10-1') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>国庆节</i></li>"
                // } else if (month + '-' + i == '11-11') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>光棍节</i></li>"
                // } else if (month + '-' + i == '12-24') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>平安夜</i></li>"
                // } else if (month + '-' + i == '12-25') {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span><i>圣诞节</i></li>"
                // } else {
                //     html += "<li data-jr=" + month + "-" + i + " data-id=" + time + " data-date=" + year + "-" + month + "-" + i + "><span>" + i + "</span></li>"
                // }
            }
            // $('.date ul').html(html);
        },
    }
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import 'calendar.css'

</style>
