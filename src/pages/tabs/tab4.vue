<template>
    <div>
        <Button size="large" @click="fetchDate">自定义日期选择器</Button>
        <Button size="large" @click="load">自定义城市选择器</Button>
        <div class="flex-1"></div>
        <div class="sheet">
            <div class="flex-box sheet-header">
                <div class="Btn"></div>
                <div id="title" class="flex-1">请选择日期</div>
                <div
                    class="Btn"
                    tapmode="touched"
                    onclick="fnCompleteBtnTouched();"
                >
                    完成
                </div>
            </div>
            <div class="sheet-body">
                <div class="title flex-box">
                    <span class="flex-1">年</span>
                    <span class="flex-1">月</span>
                    <span class="flex-1">日</span>
                </div>
                <div id="picker-container" class="flex-box flex-column">
                    <div class="flex-1"></div>
                    <div class="cell"></div>
                    <div class="flex-1"></div>
                </div>
            </div>
            <div class="cancel" tapmode="touched" @click="fnCancelBtnTouched">
                取消
            </div>
        </div>
    </div>
</template>
<script>
import { TAOBAO } from "@/libs/api";
import { memory } from "@/libs/utils";
import { Button, Toast } from "vant";
let UICustonPicker;
let vPickerId;
export default {
    name: "tab3",
    components: { Button, Toast },
    data() {
        return {
            suggests: ""
        };
    },
    methods: {
        async load() {
            Toast.loading({
                mask: true,
                forbidClick: true,
                duration: 0,
                message: "Loading..."
            });
            try {
                let suggests = await TAOBAO.get(
                    "https://suggest.taobao.com/sug?code=utf-8&q=华为"
                );

                // Example for the global data
                memory.set("suggests", suggests);
                this.suggests = memory.get("suggests");

                Toast.clear();
            } catch (e) {
                Toast.fail(e.msg);
            }
        },
        fnCancelBtnTouched() {
            this.$ac.execScript({
                // 调用上级页面方法来关闭选择器
                name: this.$ac.pageParam.cb_win,
                frameName: this.$ac.pageParam.cb_frm,
                script: "fnCloseSheetFrame();"
            });
        },
        fetchDate() {
            UICustonPicker = this.$ac.require("UICustomPicker");
            let tY = this.$ac.winHeight - 184 - 10;
            let tW = this.$ac.frameWidth - 40;
            let tNow = new Date();
            let tYear = tNow.getFullYear();
            let tMonth = tNow.getMonth();
            let tDate = tNow.getDate();
            let tMinYear = tYear - 100;
            let tMaxYear = tYear + 100;
            UICustonPicker.open(
                {
                    rect: {
                        x: 20,
                        y: tY,
                        w: tW,
                        h: 135
                    },
                    styles: {
                        bg: "rgba(61,61,61,0.0)",
                        normalColor: "rgba(61,61,61,0.5)",
                        selectedColor: "#3d3d3d",
                        selectedSize: 28,
                        tagColor: "#3685dd",
                        tagSize: 16
                    },
                    data: [
                        { scope: tMinYear + "-" + tMaxYear },
                        { scope: "1-12" },
                        { scope: "1-31" }
                    ],
                    autoHide: false,
                    loop: true,
                    rows: 3,
                    fixedOn: api.frameName,
                    fixed: true
                },
                (ret, err) => {
                    if (ret) {
                        if ("number" == typeof ret.id) {
                            vPickerId = ret.id; // 记录当前模块的ID
                        }
                        if ("show" === ret.eventType) {
                            // 设置当前时间为默认值
                            var tDefault = [tYear, tMonth + 1, tDate];
                            // fnSetSelectedValue(tDefault);
                        }
                        if ("selected" === ret.eventType) {
                            //判断选择值的合法性
                            // fnCheckSelectedValue(ret.data);
                        }
                    }
                }
            );
        }
    }
};
</script>
<style scoped>
.sheet {
    background: #fff;
    position: absolute;
    bottom: 0px;
    left: 0px;
    width: 100%;
}

.sheet-header {
    height: 50px;
    font-size: 16px;
    color: #727272;
    text-align: center;
    position: relative;
    line-height: 50px;
    box-shadow: 0px -1px 1px 1px rgba(155, 155, 155, 0.5);
}

.Btn {
    width: 50px;
    margin: 0px 20px;
    color: #e0474c;
    font-size: 14px;
}

.sheet-body {
    position: relative;
}

.sheet-body:before {
    content: "";
    display: block;
    position: absolute;
    top: 0px;
    left: 0px;
    height: 1px;
    width: 100%;
    background-color: #cccccc;
    -webkit-transform: scale(1, 0.5);
    transform: scale(1, 0.5);
    -webkit-transform-origin: center top;
    transform-origin: center top;
}

.sheet-body:after {
    content: "";
    display: block;
    height: 1px;
    width: 100%;
    position: absolute;
    background-color: #cccccc;
    bottom: 0px;
    left: 0px;
    -webkit-transform: scale(1, 0.5);
    transform: scale(1, 0.5);
    -webkit-transform-origin: center bottom;
    transform-origin: center bottom;
}

.title {
    margin: 0px 20px;
}

.title span {
    color: #727272;
    font-size: 14px;
    line-height: 50px;
    display: block;
    text-align: center;
}

#picker-container {
    height: 135px;
}

.cell {
    height: 45px;
    line-height: 48px;
    font-size: 0.7rem;
    position: relative;
    margin: 0px 20px;
    box-sizing: border-box;
}

.cell:before {
    content: "";
    display: block;
    height: 1px;
    width: 100%;
    position: absolute;
    background-color: #cccccc;
    top: 0px;
    left: 0px;
    -webkit-transform: scale(1, 0.5);
    transform: scale(1, 0.5);
    -webkit-transform-origin: center top;
    transform-origin: center top;
}

.cell:after {
    content: "";
    display: block;
    height: 1px;
    width: 100%;
    position: absolute;
    background-color: #cccccc;
    bottom: 0px;
    left: 0px;
    -webkit-transform: scale(1, 0.5);
    transform: scale(1, 0.5);
    -webkit-transform-origin: center bottom;
    transform-origin: center bottom;
}

.cancel {
    height: 50px;
    line-height: 50px;
    font-size: 14px;
    color: #727272;
    text-align: center;
    border-top: 10px solid #eeeeee;
}
</style>
