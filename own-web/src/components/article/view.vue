<template>
<div class="row">
    <mycommon v-bind:message="parentMsg"></mycommon>
    <div class="col-sm-12 top">
        <input class="serchtext" type="text" placeholder="输入你要查找的内容" >
        <span class="search" v-on:click="search()">搜索</span>
    </div>
    <div class="col-sm-12  home-con-right" v-for="(item,index) in homelists" :key="index">
            <div class="row">
                <h3 style="margin-left:15px">{{item.title}}</h3>
                <div class="col-sm-12"><span class="articleremove" v-on:click="clickremove(homelists[index])">x</span></div>
                <div class="col-sm-3"><span class="articlesort" >{{item.sort}}</span></div>
                <div class="col-sm-3"><span class="articletime" >{{item.time}}</span></div>
                <div class="col-sm-12"><span class="articlesort" >{{item.con.substring(0,100)}}</span></div>
            </div>
            
    </div>
    <!--分页-->
    <div class="fenye">
        <span style="height:20px;border:1px solid #000;cursur:pointer;display:none">上一页</span>
        <div class="page" v-for="(item,index) in sumpage" :key="index">
            <span :class="{'pageactive':ind===index}" v-on:click="clickpage(item,index)">{{index+1}}</span>
        </div>
        <span style="height:20px;border:1px solid #000;cursor:pointer;display:none">下一页</span>
    </div>
</div>
</template>
<script>
import Vue from 'vue'
import common from '../owncommon.vue'
export default {
    data(){
        return {
            homelists:[] ,
            sumpage:[],
            ind:'',
            parentMsg:'article'
        }
       
    },
    components:{
        'mycommon':common
    },
    mounted:function () {
        //类似于jquery中的ready方法
        this.sums();
    },
    methods:{ 
        //渲染数据
        showlist(params){
            var _this=this;
            _this.sumpage=[];
            this.$http.post('/api/list/showlist',params).then((response)=>{
                //列表数据
                var result=JSON.parse(response.bodyText).data;
                console.log(result)
                //数据的总数量
                var sum=JSON.parse(response.bodyText).sum;
                //渲染出页码
                $(".fenye .page").empty();
                
                for(var i=0;i<Math.ceil(sum/params.limit);i++){
                    _this.sumpage.push(i);
                }
				//将结果赋值给需要循环
				_this.homelists=result;
				return _this.homelists;
			});
        },
        //渲染列表
        sums(){
			var _this=this;
            var params={
                page:0,
                limit:5
            };
			this.showlist(params);
		},
       
        //查找内容
        search(){
            var value=$(".serchtext").val();
            //点击的是第几页
            var params={
                page:0,
                limit:5,
                title:value
            };
            this.showlist(params);
        },
        //点击分页
        clickpage(index){
            var _this=this;
            var value=$("input").val();
            this.ind=index;
            //点击的是第几页
            var params={
                page:index,
                limit:5,
                title:value
            };
            this.showlist(params);
        },
         //删除数据
        clickremove(i){
            alert("删除成功！");
            var obj=i._id;
            var params={
                "_id":obj
            };
			this.$http.post('/api/list/removelist',params);
            this.sums();
        },
       

    }
}
</script>
<style>
/*搜索*/
.pageactive{
    background:#000;
    color:#fff;
}
.top{
    height:60px;
}
.top .serchtext{
    height:40px;
    margin-top:10px;
    padding:0px 10px;
    border:1px solid #ccc;
    font-size:12px;
    width:300px;
    float:left;
}
.top .search{
    width:60px;
    height:40px;
    margin-top:10px;
    background:#efefef;
    border:1px solid #ccc;
    line-height:40px;
    text-align:center;
    font-size:16px;
    display:block;
    cursor:pointer;
    float:left;
}
.top .search:hover{
    color:#fff;
    background:#afb5b5
}
/*每個數據*/
.home-con-right{
    height:150px;
    border:1px solid #99cccc;
    padding:10px;
    cursor:pointer
}
.home-con-right:hover{
    background:#99CC99
}
.home-con-right h2{
    margin-top:10px;
}
.home-con-right .articleremove{
    display:block;
    width:15px;
    height:15px;
    text-align:center;
    line-height:12px;
    border:1px solid #ccc;
    background:#fff;
    color:#999;
    position:absolute;
    top:20px;
    right:20px;
    cursor:pointer;
}
.home-con-right .articleremove:hover{
    background:#999;
    color:#fff;
}
.home-con-right .articlesort{
    margin-top:20px;
}
/*分頁*/
.page span{
    width:20px;
    height:20px;
    line-height:20px;
    text-align:center;
    border:1px solid #000;
    displaY:block;
    floaT:left;
    cursor:pointer;
    margin:5px}
.page span:hover{
    background:#000;
    color:#fff}

</style>