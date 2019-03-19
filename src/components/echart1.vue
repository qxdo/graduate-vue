<template>
    <div id="father">
        <div id="main1" style="width: 500px;height: 289px; box-sizing:border-box; line-height: 289px;">

        </div>
        <div id="main2" style="width: 500px;height: 289px; box-sizing:border-box; line-height: 289px;">

        </div>

    </div>

</template>

<script>


    export default {
        name: "echart",
        data() {
            return {
                data1: [],
                data2: [],
            }
        },



        mounted() {
            var _this = this;
            var echarts = require('echarts');

            // 基于准备好的dom，初始化echarts实例
            var myChart = echarts.init(document.getElementById('main1'));
            //绘制图表
            myChart.showLoading();
            myChart.setOption({
                title: {
                    text: '每日用户增量图'
                },
                tooltip: {},
                xAxis: {
                    data: [],
                },
                yAxis: {},
                series: [{
                    name: '',
                    type: 'bar',
                    data: [],
                }]
            });


            _this.$axios
                .get('/charts/peerdayuserincreased')
                .then(function (res) {
                    if (res.data.status === 202) {
                        console.log(res.data.data);
                        var tmp = res.data.data;
                        tmp.forEach(function (v) {
                            _this.data1.push(v.title);
                            _this.data2.push(v.data);

                        })
                        myChart.hideLoading();

                        myChart.setOption({

                            xAxis: {
                                data: _this.data1,
                            },
                            series: [{
                                name: '时间',
                                type: 'bar',
                                data: _this.data2,
                            }]
                        });

                    }
                })






        }
    }
</script>

<style scoped>

</style>
