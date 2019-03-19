<template>
    <!--homepage 是整个页面 -->
    <div id="homepage" style="width: 100%;height: 100%;">
        <div id="home" style="width: 25%;height: 100%;">


        </div>
        <div id="showpage" :is="currentView" style="width: 75%;height: 100%;float: right;">

        </div>
        <menubar @menuSelectd="changeMenu"></menubar>


    </div>
</template>

<script>
    import menubar from '@/components/menubar'
    import sidepageindex from '@/components/sidepageindex'


    import customer from '@/components/customer/customer'

    import user from '@/components/user/user'

    import node from '@/components/node/node'

    import echart from '@/components/echart1'


    export default {
        name: "home",


        data() {
            return {
                sidepageindex: 'sidepageindex',
                customer: 'customer',
                user: 'user',
                echarts: 'echart',
                node: 'node',
                currentView: 'sidepageindex',

                // 默认选中欢迎页


            };
        },

        methods: {
            //变更右侧DIV 所显示的组件
            changeMenu: function (v) {
                this.currentView = v;
            }

        },
        components: {
            menubar,
            sidepageindex,
            customer,
            user,
            node,
            echart,


        }


    }
</script>

<style scoped>

</style>
