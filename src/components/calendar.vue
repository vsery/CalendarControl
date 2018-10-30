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
                <a href="JavasSript:" class="add" @click="_addJourney()">添加</a>
            </div>
            <!-- <ul class="calendar-format">
                <li @click="_setType('week')" :class="options.type == 'week'? 'active':''">周历</li>
                <li @click="_setType('month')" :class="options.type == 'month'? 'active':''">月历</li>
                <li @click="_setType('list')" :class="options.type == 'list'? 'active':''">列表</li>
            </ul> -->
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
                    <li @click="_addJourney(index)">
                        <calendarView ref="calendar" :year="item.year" :month="item.month" :days="item.days" :curr="item.isCurr"></calendarView>
                        <!-- <div class="data-content-box"> -->
                        <template v-if="item.note.length > 0">
                            <div class="data-content-item" v-for="Citem, Cindex in item.note">
                                <div class="data-content" @click.stop="_openJourney(Citem)">
                                    <span class="content-name">{{Citem.name}}</span>
                                    <span class="content-body">{{Citem.content}}</span>
                                </div>
                                <div class="content-del" @click.stop="_delJourney(index, Cindex)"> 删<br>除 </div>
                            </div>
                        </template>
                        <!-- </div> -->
                    </li>
                </template>
            </ul>
        </div>
        <calendarConView ref="journeyBox" :journeyData="journey" @close="_closeJourney" @save="_saveJourney" v-if="journeyisShow"></calendarConView>
    </div>
</template>
<script>
import calendarDate from '@/components/calendarDate' // 时间过滤
import calendarContent from '@/components/calendarContent' // 行程编辑框
export default {
    name: 'calendar',
    components: {
        'calendarView': calendarDate,
        'calendarConView': calendarContent,
    },
    data() {
        return {
            journey: null,
            journeyisShow: false,
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
                // { year: 2018, month: 8, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 13, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 15, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 17, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 8, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                // { year: 2018, month: 8, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                // { year: 2018, month: 9, day: 21, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 21, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 13, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 13, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 9, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                // { year: 2018, month: 9, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                // { year: 2018, month: 10, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 10, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                // { year: 2018, month: 10, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                // { year: 2018, month: 11, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 11, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 15, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 15, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 11, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                // { year: 2018, month: 11, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },

                // { year: 2018, month: 12, day: 1, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 2, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 3, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 4, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 5, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 6, name: "约会:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 7, name: "美女生日:上午看电影", content: '陪美女出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                // { year: 2018, month: 12, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
                // { year: 2018, month: 12, day: 23, name: "老爸结婚纪念日", content: '老爸,老妈40年结婚纪念日,亚哈酒店5楼洞庭厅12:00' },
            ],
        }
    },
    created() {
        this._getData();
        this.options.noon = this.options.time.slice(0, 2); //当前时间的 上午|下午
    },
    mounted() {
        this.$nextTick(function() {
            this._initData();
        });
    },
    methods: {
        /**
         * [_getData 加载行程]
         * @return {[type]} [行程数组]
         * 注: 进入时. 调用AJAX, 获取行程数据
         */
        _getData() {
            this.weekLists = [
                { year: 2018, month: this.options.month, day: 15, name: "老婆生日:上午看电影", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: this.options.month, day: 15, name: "老婆生日:下午出去玩", content: '陪老婆出去看电影, 喜盈门范城5楼电影院' },
                { year: 2018, month: this.options.month, day: 22, name: "外婆生日", content: '去外婆家玩,蹭饭' },
            ]
        },
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
            // let showYear = year + '年', showMonth = this.months[month - 1] + '月', showDay = this.days[date - 1] + '日';
            // console.log(showYear, showMonth, showDay);
            let fristdays = new Date(year, month - 1, 0).getDate(); // 计算上月有多少天；
            let days = new Date(year, month, 0).getDate(); // 计算本月有多少天；
            let lastdays = new Date(year, month + 1, 0).getDate(); // 计算下月有多少天；
            let week = new Date(year, month - 1, 0).getDay(); // 本月第一天 周几
            let lastWeek = new Date(year, month - 1, days).getDay(); // 本月最后一天 周几
            var _temp = []; // 零时数据
            // 构建 当前月 数据
            for (var i = 1; i <= days; i++) {
                let _data = {
                    year: year,
                    month: month,
                    days: i,
                    isCurr: true,
                    note: [],
                };
                _temp.push(_data);
            }
            // 构建 上个月 数据
            if (week != 1 && this.options.type == 'month') {
                for (var i = 0; i < week; i++) {
                    let _data = {
                        year: year, // 年
                        month: month - 1, // 月
                        days: fristdays - i, // 日
                        isCurr: false, // 当月
                        note: [], // 行程
                    };
                    _temp.unshift(_data);
                }
            }
            // 构建 下个月 数据
            if (lastWeek != 0 && this.options.type == 'month') {
                for (var i = 1; i <= 7 - lastWeek; i++) {
                    let _data = {
                        year: year, // 年
                        month: month + 1, // 月
                        days: i, // 日
                        isCurr: false, // 当月
                        note: [], // 行程
                    };
                    _temp.push(_data);
                }
            }
            // 给显示数据添加行程
            const ws = this.weekLists;
            for (var Xkey = 0; Xkey < ws.length; Xkey++) {
                for (var Key = 0; Key < _temp.length; Key++) {
                    if (_temp[Key].year == ws[Xkey].year && _temp[Key].month == ws[Xkey].month && _temp[Key].days == ws[Xkey].day) {
                        _temp[Key].note.push(ws[Xkey]);
                    }

                }
            }
            this.dataList = _temp; // 复制, 渲染
        },
        // 切换月份, 今天
        _setMonth: function(type) {
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
            this._initData(this.options.year, this.options.month);
        },
        // 设置展示类型
        _setType: function(type) {
            this.options.type = type; // 设置显示类型
        },
        // 添加行程
        _addJourney(index) {
            if (index != null) {
                this.journey = {
                    year: this.dataList[index].year,
                    month: this.dataList[index].month,
                    day: this.dataList[index].days,
                    isEdit: false
                }
            }
            this.journeyisShow = true; // 打开行程弹框
            this.$nextTick(function() {
                this.$refs.journeyBox._loadDate(); // 渲染弹框
            });
        },
        // 打开行程
        _openJourney(item) {
            this.journeyisShow = true;
            if (this.journey == null) {
                this.journey = item; // 选择行程 => 当前行程
                this.$nextTick(function() {
                    this.$refs.journeyBox._loadDate(); // 渲染弹框
                });
            }
        },
        // 删除行程
        _delJourney(index, key) {
            this.dataList[index].note.splice(key, 1); // 删除显示数据中行程
        },
        // 保存行程
        _saveJourney(res) {
            if (!res.isEdit) {
                for (var Key = 0; Key < this.dataList.length; Key++) {
                    if (this.dataList[Key].year == res.year && this.dataList[Key].month == res.month && this.dataList[Key].days == res.day) {
                        this.dataList[Key].note.push(res);
                    }
                }
            }
            this._closeJourney();
            this._saveData();
        },
        // 关闭行程
        _closeJourney() {
            this.journey = null; // 清空当前行程
            this.journeyisShow = false; // 关闭行程弹框
        },
        // 保存行程到数据库
        _saveData() {
            const _temp = [];
            for (var i = 0; i < this.dataList.length; i++) {
                if (this.dataList[i].note.length > 0) {
                    _temp.push.apply(_temp, this.dataList[i].note);
                }
            }
        }
    },
    // 离开页面时,保存数据
    beforeDestroy() {
        // this._saveData();
    }
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import 'calendar.css'

</style>
