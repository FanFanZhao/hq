<template>
    <div class="detail">
        <div v-for="(item,index) in detailList">
            <p>客服：</p>
            <div>
                <p>内容下单想</p>
            </div>
            <p>2018333</p>
        </div>
    </div>
</template>
<script>
export default {
    name:"orderDetail",
    data(){
        return{
            detailList:[],
        }
    },
    created(){
        this.id = this.$route.query.id;
        this.token=localStorage.getItem('token');
    },
    methods:{
        getDetail(){
            var that =this;
            this.$http({
                url: '/api/' + 'work_order/detail/' + that.id,
                method:'get',
                data:{},
                headers: { Authorization: that.token }
            }).then(res=>{
                console.log(res)
                var msg=res.data.message.data;
                that.detailList=msg;
                // if(res.data.type ==='ok'){
                //     that.hisList=msg;
                // }else{
                //     layer.msg(res.data.message);
                // }
            })
            .catch(error=>{
                console.log(error)
            })
        },
    },
    mounted(){
        this.getDetail()
    }
}
</script>
<style>
.detail{
    min-height: 850px;
}
</style>


