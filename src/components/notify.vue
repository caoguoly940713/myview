<template>
    <div>
        <headerComponent></headerComponent>

        <div style='margin: 20px 0 20px 0'>
            公告板
            <a href='http://localhost:8080/#/index' style='margin: 0 20px 0 20px'>主页</a>
        </div>

        <div>
            <div style='margin-bottom: 20px'>
                标题：<input placeholder="请输入标题" v-model="title" style='margin-right: 20px' size='35'/></br>

                <br>内容：<br><textarea placeholder="请输入内容" cols='40' rows='5' v-model="content" style='margin-right: 20px'/></br></br>

                <button v-on:click='createNotify'>新建公告</button>

                <div style='margin-top: 20px'>{{message}}</div>
            </div>
        </div>

        =============================================================

        <div v-for="item in response" style='margin: 10px 0 10px 0'>
            <div>标题：{{ item.title }}</div>
            <div style='width: 500px'>内容：{{ item.content }}</div>
            <div>发布人：{{ item.author }}</div>
            <div>发布日期：{{ item.publishDate }}</div>
            <button class='mgl' v-on:click="deleteNotify(item)">删除公告</button>
        </div>

        =============================================================

        <div style='margin-bottom: 200px'></div>
    </div>

</template>

<script>
    import axios from 'axios'; 
    import headerComponent from './header.vue';

    export default({
        name: 'index',

        data:  function(){
                return {
                    response: [],
                    title: '',
                    content: '',
                    message: ''
                }
            },
        
        components: {
            headerComponent
        },

        created: function () {
            this.getNotifies();
        },

        methods: {
            
            getNotifies:  function() {
                axios.get("http://localhost:8082/getAllNotifies")
                    .then((res)=>{
                        console.log("getNotifies", res.data);

                        if(res.data.success){
                            this.response = res.data.data;
                        }else{
                            alert(res.data.message);
                        }
                    }) 
            },

         
            logout: function() {
                axios.get("http://localhost:8082/logout").then(
                    (res)=>{ 
                        console.log("delete", res.data); 
                        this.$router.push({path:'/login'})
                }) 
            },

            createNotify: function(){
                this.message = '';

                const notifyParams = {
                    params: { title: this.title, content: this.content }
                };

                axios.get("http://localhost:8082/createNotify", notifyParams).then(
                    (res)=>{ 
                        
                        if(res.data.success){
                            this.message = res.data;
                            this.getNotifies();

                        }else{
                            console.log('createNotify', res.data);
                        }
                }) 

            },

            deleteNotify: function(item){
                const notifyParams = { params: { id: item.id } };

                axios.get("http://localhost:8082/deleteNotify", notifyParams).then(
                    (res)=>{ 
                        
                        if(res.data.success){
                            this.getNotifies();
                        }else{
                            console.log('deleteNotify', res.data);
                        }
                }) 

            },
        }
    });
</script>

<style>
  .out-line {
    display: flex;
  }

  .mgl{
    margin-top: 10px;
  }

</style>