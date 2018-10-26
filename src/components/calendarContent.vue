<!--  -->
<template>
    <div id="calendarConBox">
        <div class="box-content">
            <el-form ref="form" :model="conData" label-width="80px" v-if="conData != null">
                <el-form-item label="行程时间">
                    <el-date-picker v-model="currDate" type="date" placeholder="选择日期"> </el-date-picker>
                </el-form-item>
                <el-form-item label="行程名称">
                    <el-input v-model="conData.name"></el-input>
                </el-form-item>
                <el-form-item label="行程内容">
                    <el-input type="textarea" :autosize="{ minRows: 2, maxRows: 4}" v-model="conData.content"></el-input>
                </el-form-item>
                <el-form-item label-width="0" class="tamb">
                    <el-button type="primary" @click="_onSave">保存</el-button>
                    <el-button @click="_onClose">取消</el-button>
                </el-form-item>
            </el-form>
        </div>
        <div class="box-bg" @click="_onClose"></div>
    </div>
</template>
<script>
export default {
    name: '',
    data() {
        return {
            // conData: null,
            conData: {
                name: null,
                content: null,
                year: null,
                month: null,
                day: null,
                isEdit: false
            },
            currDate: new Date(),
        }
    },
    props: {
        'journeyData': {
            type: Object,
            default: null
        }
    },
    mounted() {
        this.$nextTick(function() {});
    },
    methods: {
        // 显示数据
        _loadDate() {
            if (this.journeyData != null) {
                this.conData = this.journeyData;
                if(this.conData.isEdit != false) this.conData.isEdit = true;
                this.currDate = new Date(this.conData.year, this.conData.month -1, this.conData.day);
            }
        },
        // 提交数据
        _onSave() {
            this.conData.year = this.currDate.getFullYear();
            this.conData.month = this.currDate.getMonth() + 1;
            this.conData.day = this.currDate.getDate();
            this.$emit('save', this.conData);
        },
        // 放弃编辑
        _onClose() {
            this.$emit('close');
        }
    }
}

</script>
<style scoped>
#calendarConBox .box-bg {
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, .7);
    z-index: 1080;
}

#calendarConBox .box-content {
    position: fixed;
    left: 50%;
    top: 50%;
    width: 40rem;
    min-height: 19rem;
    margin: -12rem 0 0 -20rem;
    box-sizing: border-box;
    padding: 2rem;
    background-color: white;
    z-index: 1081;
    border-radius: .5rem;
    overflow: hidden;
}

#calendarConBox .el-date-editor {
    width: 100%;
}
#calendarConBox .tamb{
    text-align: center;
    margin-bottom: 0;
}
</style>
