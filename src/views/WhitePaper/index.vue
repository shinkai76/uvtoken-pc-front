<!-- -->
<template>
    <div class="page">
        <div class="no_data">
            <img class="no_data_img" src="@/assets/img/no_data.png" alt="">
            <p class="no_data_hint">等待主网上线后开通</p>
        </div>
    </div>
</template>

<script>
export default {
    beforeRouteEnter(to, from, next) {
      next(vm => {
        vm.$router.push(from)
        window.open(vm.$BaseUrl+ 'material/whitepaper.pdf', '_blank')
      })
    },
    name: '',
    data() {
       return {

       }
    },
    computed:{

    },
    watch:{

    },
    methods:{

    },
    created(){

    },
    mounted(){

    }
}
</script>

<style lang='less' scoped>
    .page{
        height: 100%;
        background: rgb(242, 242, 242);
        overflow: hidden;
        .no_data{
            width: 500px;
            height: 800px;
            text-align: center;
            margin: 200px auto 0;
            .no_data_img{
                width: 140px;
                height: 120px;
            }
            .no_data_hint{
                color: #999999;
                font-size: 48px;
            }
        }
    }
</style>
