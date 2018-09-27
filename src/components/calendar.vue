<template>
    <div id="calendar-control">
        <div id="calendar-header">
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
            <ul id="calendar-format">
                <li :class="options.type == 'week'? 'active':''">列表</li>
                <li :class="options.type == 'month'? 'active':''">月历</li>
                <li :class="options.type == 'list'? 'active':''">周历</li>
            </ul>
        </div>
        <table cellspacing="0" cellpadding="0" width="100%" height="38" class="js_tb_head">
            <tbody>
                <tr class="calendar_th">
                    <th height="38">周一</th>
                    <th>周二</th>
                    <th>周三</th>
                    <th>周四</th>
                    <th>周五</th>
                    <th class="weekend">周六</th>
                    <th class="weekend">周日</th>
                </tr>
            </tbody>
        </table>
    </div>
</template>
<script>
export default {
    name: 'calendar',
    data() {
        return {
            newDate: new Date(),
            options: {
                noon: 'afternoon', // forenoon[上午], afternoon[下午]
                fullyear: new Date().getFullYear(),
                month: new Date().getMonth() + 1,
                day: new Date().getDate(),
                year: new Date().getYear(), //获取当前年份(2位)
                date: new Date().getDate(), //获取当前日(1-31)
                day: new Date().getDay(), //获取当前星期X(0-6,0代表星期天)
                time: new Date().getTime(), //获取当前时间(从1970.1.1开始的毫秒数)
                hours: new Date().getHours(), //获取当前小时数(0-23)
                minutes: new Date().getMinutes(), //获取当前分钟数(0-59)
                seconds: new Date().getSeconds(), //获取当前秒数(0-59)
                milliseconds: new Date().getMilliseconds(), //获取当前毫秒数(0-999)
                toLocaleDateString: new Date().toLocaleDateString(), //获取当前日期
                time: new Date().toLocaleTimeString(), //获取当前时间
                timeStr: new Date().toLocaleString(), //获取日期与时间
                type: 'month', // week[周], month[月], list[列表]
                oneday: 'monday', // monday[一], sunday[日]
            },
            currentTime: null, // 当前时间
            fullDate: {

            },
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
    created() {},
    beforeMount() {},
    mounted() {
        console.log(this.options);
        this.$nextTick(function() {});
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
        initData: function() {

        },
    }
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#calendar-control {
    width: auto;
    margin: 0 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
}

#calendar-control * {
    box-sizing: border-box;
}

#calendar-control a {
    text-decoration: none;
}

#calendar-header {
    height: 40px;
    line-height: 40px;
    background-color: #00bff3;
    border-bottom: 2px solid #00acda;
    color: white;
    text-align: left;
    border-radius: 5px 5px 0 0;
}

.current-time {
    margin: 0 15px;
    display: inline-block;
}

.sys-time-btn {
    display: inline-block;
}

.sys-time-btn a {
    height: 24px;
    line-height: 24px;
    padding: 0 10px;
    margin-left: 10px;
    border: 1px solid #d0d0d0;
    border-radius: 4px;
    font-size: 11px;
    display: inline-block;
    color: rgba(255, 255, 255, .8);
    background-color: rgba(255, 255, 255, .2);
}

.sys-time-btn a:hover {
    color: #48ffff;
}


#calendar-format {
    float: right;
    list-style: none;
    margin: 0;
    padding: 0;
    height: 30px;
    line-height: 30px;
    width: auto;
    margin: 5px 15px;
    border: 1px solid #d0d0d0;
    background: rgba(0, 0, 0, .3);
    border-radius: 5px;
}

#calendar-format li {
    float: left;
    padding: 0 10px;
    cursor: pointer;
    font-size: 11px;
    height: 100%;
    color: rgba(255, 255, 255, .7);
}

#calendar-format li:first-child {
    border-radius: 5px 0 0 5px;
}

#calendar-format li:last-child {
    border-radius: 0 5px 5px 0;
}

#calendar-format li.active {
    background-color: #00b0f0;
    color: white;
}

#calendar-format li:hover {
    color: #00bff3;
}

#calendar-format li~li {
    margin-left: 5px;
}

#calendar-format {}

</style>
