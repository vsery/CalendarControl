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
                <template v-for="tab, index in dataList" v-if="dataList.length > 0">
                    <li>
                        <dl>
                            <dt>
                                <template v-if="tab.fest == null ">
                                    <span class="lunar">{{tab.lunar}}</span>
                                </template>
                                <template v-else>
                                    <span class="fest" :title="tab.fest">{{tab.fest}}</span>
                                </template>
                                <span class="day">{{tab.day}}</span>
                            </dt>
                            <dd>
                                <!-- {{tab}} -->
                            </dd>
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
            currentDate: new Date(), // 当前时间对象
            currentTime: null, // 当前时间
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
            fullDate: {},
            userData: {},
            // 月份数组
            months: ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二'],
            // 日期数组
            days: ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二', '十三', '十四', '十五', '十六', '十七', '十八', '十九', '二十', '二十一', '二十二', '二十三', '二十四', '二十五', '二十六', '二十七', '二十八', '二十九', '三十', '三十一', '三十二'],
            // 星期数组
            weeks: ['一', '二', '三', '四', '五', '六', '日'],
            // 传统节日
            festival: [
                { month: 1, day: 1, name: "元旦" },
                { month: 2, day: 2, name: "世界湿地日" },
                { month: 2, day: 14, name: "情人节" },
                { month: 3, day: 3, name: "全国爱耳日" },
                { month: 3, day: 5, name: "青年志愿者服务日" },
                { month: 3, day: 8, name: "国际妇女节" },
                { month: 3, day: 9, name: "保护母亲河日" },
                { month: 3, day: 12, name: "中国植树节" },
                { month: 3, day: 14, name: "白色情人节" },
                { month: 3, day: 15, name: "世界消费者权益日" },
                { month: 3, day: 21, name: "世界森林日" },
                { month: 3, day: 22, name: "世界水日" },
                { month: 3, day: 23, name: "世界气象日" },
                { month: 3, day: 24, name: "世界防治结核病日" },
                { month: 4, day: 1, name: "愚人节" },
                { month: 4, day: 7, name: "世界卫生日" },
                { month: 4, day: 22, name: "世界地球日" },
                { month: 4, day: 26, name: "世界知识产权日" },
                { month: 5, day: 1, name: "国际劳动节" },
                { month: 5, day: 3, name: "世界哮喘日" },
                { month: 5, day: 4, name: "中国青年节" },
                { month: 5, day: 8, name: "世界红十字日" },
                { month: 5, day: 12, name: "国际护士节" },
                { month: 5, day: 15, name: "国际家庭日" },
                { month: 5, day: 17, name: "世界电信日" },
                { month: 5, day: 20, name: "全国学生营养日" },
                { month: 5, day: 23, name: "国际牛奶日" },
                { month: 5, day: 31, name: " 世界无烟日" },
                { month: 6, day: 1, name: " 国际儿童节" },
                { month: 6, day: 5, name: "世界环境日" },
                { month: 6, day: 6, name: "全国爱眼日" },
                { month: 6, day: 17, name: "世界防治荒漠化和干旱日" },
                { month: 6, day: 23, name: "国际奥林匹克日" },
                { month: 6, day: 25, name: "全国土地日" },
                { month: 6, day: 26, name: "国际禁毒日" },
                { month: 7, day: 1, name: "中国共产党诞生日" },
                { month: 7, day: 7, name: "中国人民抗日战争纪念日" },
                { month: 7, day: 11, name: "世界人口日" },
                { month: 8, day: 1, name: "中国人民解放军建军节" },
                { month: 8, day: 12, name: "国际青年节" },
                { month: 9, day: 8, name: "国际扫盲日" },
                { month: 9, day: 10, name: "中国教师节" },
                { month: 9, day: 16, name: "中国脑健康日" },
                { month: 9, day: 20, name: "全国爱牙日" },
                { month: 9, day: 21, name: "世界停火日" },
                { month: 9, day: 27, name: "世界旅游日" },
                { month: 10, day: 1, name: "中华人民共和国国庆节" },
                { month: 10, day: 4, name: "世界动物日" },
                { month: 10, day: 5, name: "世界教师日" },
                { month: 10, day: 8, name: "全国高血压日" },
                { month: 10, day: 9, name: "世界邮政日" },
                { month: 10, day: 10, name: "世界精神卫生日" },
                { month: 10, day: 14, name: "世界标准日" },
                { month: 10, day: 15, name: "国际盲人节" },
                { month: 10, day: 16, name: "世界粮食日" },
                { month: 10, day: 17, name: "国际消除贫困日" },
                { month: 10, day: 24, name: "联合国日" },
                { month: 10, day: 28, name: "中国男性健康日" },
                { month: 10, day: 29, name: "国际生物多样性日" },
                { month: 10, day: 31, name: "万圣节" },
                { month: 11, day: 8, name: "中国记者节" },
                { month: 11, day: 9, name: "消防宣传日" },
                { month: 11, day: 14, name: "世界糖尿病日" },
                { month: 11, day: 17, name: "国际大学生节" },
                { month: 11, day: 25, name: "国际消除对妇女的暴力日" },
                { month: 12, day: 1, name: "世界爱滋病日" },
                { month: 12, day: 3, name: "世界残疾人日" },
                { month: 12, day: 4, name: "全国法制宣传日" },
                { month: 12, day: 9, name: "世界足球日" },
                { month: 12, day: 25, name: "圣诞节" },
                { month: 12, day: 29, name: "国际生物多样性日" }
            ],
            // 24节气
            solar: ['小寒', '大寒', '立春', '雨水', '惊蜇', '春分', '清明', '谷雨', '立夏', '小满', '芒种', '夏至', '小暑', '大暑', '立秋', '处暑', '白露', '秋分', '寒露', '霜降', '立冬', '小雪', '大雪', '冬至'],
            // 显示数据
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
        this.$nextTick(function() {
            this._initData();
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
        _initData: function(year, month, date) {
            if (year == null) { year = this.options.year; }
            if (month == null) { month = this.options.month; }
            if (date == null) { date = this.options.date; }
            // console.log(this.months, this.days);
            // console.log(year, month, date);
            let showYear = year + '年';
            let showMonth = this.months[month - 1] + '月';
            let showDay = this.days[date - 1] + '日';
            // console.log(year, month, date);
            // console.log(showYear, showMonth, showDay);
            // 计算本月1号是周几；
            // let week = new Date(year + '-' + month + '-1').getDay();
            // 计算本月有多少天；
            let days = new Date(year, month, 0).getDate();
            // 计算上月有多少天；
            // let dayw = new Date(year, month - 1, 0).getDate();
            // console.log(week, days, dayw);
            var _temp = []; // 零时数据
            // 构建当月数据
            for (var i = 1; i <= days; i++) {
                let _data = {
                    day: null, // 阳历
                    week: null, // 周几
                    fest: null, // 节日
                    lunar: null, // 农历
                    solar: null, // 节气
                    work: []
                };
                _data = this._getDayWeek(_data, year, month, i);
                // _data = this._getDayFest(_data, year, month, i);
                _temp.push(_data);
            }
            this.dataList = _temp; // 复制, 渲染
            // console.log(this.dataList);
        },
        // 获得当前时间: 阳历, 周几
        _getDayWeek: function(D, Y, M, R) {
            let RES = null;
            D.day = R;
            D.lunar = this.days[R >= 20 ? R - 20 : R + 5];
            let W = new Date(Y, M - 1, R).getDay();
            switch (W) {
                case 1:
                    D.week = '周一';
                    break;
                case 2:
                    D.week = '周二';
                    break;
                case 3:
                    D.week = '周三';
                    break;
                case 4:
                    D.week = '周四';
                    break;
                case 5:
                    D.week = '周五';
                    break;
                case 6:
                    D.week = '周六';
                    break;
                case 0:
                    D.week = '周日';
                    break;
            }
            for (let i = 0; i < this.festival.length; i++) {
                if (this.festival[i].month == M && this.festival[i].day == R) {
                    D.fest = this.festival[i].name;
                }
            }
            RES = D;
            // D = this._getDayFest(D, Y, M, R);
            console.log(D);
            return RES;
        },
        // 获取当前时间: 传统节日
        _getDayFest: function(D, Y, M, R) {
            for (let i = 0; i < this.festival.length; i++) {
                if (this.festival[i].month == M && this.festival[i].day) {
                    D.fest = this.festival[i].name;
                }
            }
            return D;
        },
    }
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import 'calendar.css'

</style>
