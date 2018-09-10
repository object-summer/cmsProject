<template>
    <div class="tmpl">
        <!--  组件名navBar -->  
        <nav-bar title="图片详情"></nav-bar>
        <!-- 组件名:navbar -->
        <!--  使用：navbar-->
        <div class="photo-title">
            <p v-text="imgInfo.title"></p>
            <span>发起日期：{{imgInfo.add_time | convertDate}}</span>
            <span>{{imgInfo.click}}次浏览</span>
            <span>分类：民生经济</span>
        </div>
        <ul class="mui-table-view mui-grid-view mui-grid-9">
            <li v-for="(img,index) in imgs"  :key="index"  class="mui-table-view-cell mui-media mui-col-xs-4 mui-col-sm-3">
                <!-- <img :src="img.src"> -->
                 <img class="preview-img" :src="img.src" height="100" @click="$preview.open(index, imgs)">
            </li>
           
        </ul>
        <div class="photo-desc">
            <p v-html="imgInfo.content"></p>
        </div>

        <!-- 评论内容开始 -->
        <div class="photo-bottom">
            <ul>
                <li class="photo-comment">
                    <div>
                        <span>提交评论</span>
                        <span><a @click="goBack">返回</a></span>
                    </div>
                </li>
                <li class="txt-comment">
                    <textarea v-model="msg"></textarea>
                </li>
                <li>
                	<mt-button type='danger' size='large' @click="senComment">发表评论按钮</mt-button>
                </li>
                <li class="photo-comment">
                    <div>
                        <span>评论列表</span>
                        <span>44条评论</span>
                    </div>
                </li>
            </ul>
            <ul class="comment-list">
                <li v-for="(comment,index) in comments" :key="index">
                    {{comment.user_name}}：{{comment.content}} {{comment.add_time|convertDate}}
                </li>
            </ul>
            <mt-button type='danger' size='large' plain @click="loadByPage">加载更多按钮</mt-button>
        </div>

        <!-- 评论内容结束 -->



    </div>
</template>
<script>
export default {
    data(){
        return {
            imgs:[],//存放缩率图
            imgInfo:{},//详情数据对象
            comments:[],//存放评论数据
            pageIndex:1,//页码
            cid:this.$route.params.id, //记录当前图片id
            msg:'',
        }
    },
    created(){
        //1:获取路由参数
        // let pid = this.$route.params.id;
        //2:发起请求2个
        //图片详情
        this.$ajax.get('getimageInfo/' + this.cid)
        .then(res=>{
            //一个id对应一个详情对象
            this.imgInfo = res.data.message[0];
        })
        .catch(err=>{
            console.log(err)
        });
        //缩略图
        this.$ajax.get('getthumimages/' + this.cid)
        .then(res=>{
            this.imgs = res.data.message;
            //forEach
            this.imgs.forEach((ele)=>{
                ele.w=300;
                ele.h=200; //缩率图显示的高
            })
        })
        .catch(err=>{
            console.log(err)
        })
        //评论操作 开始
        this.loadFirst();
        //评论操作 结束
        //清楚数据
        this.msg = '';

    },
    methods:{
        //先调用这个函数
        loadFirst(){
            this.$ajax.get('getcomments/' + this.cid +'?pageindex=1')
            .then(res=>{
                this.comments = res.data.message;
            })
            .catch(err=>{
                console.log(err);
            })
        },
        //再点击按钮
        loadByPage(){
             this.$ajax.get('getcomments/' + this.cid +'?pageindex=' + (++this.pageIndex))
            .then(res=>{
                //追加函数concat（）
                this.comments = this.comments.concat(res.data.message);
            })
            .catch(err=>{
                console.log(err);
            })
        },
        //发表评论
        senComment(){
	        this.$ajax.post('/postcomment/'+this.cid,'content='+this.msg)
	        .then(res=>{
	        	console.log(res);
	        	//获取最新的第一页数据
	        	this.loadFirst();
	        })
	        .catch(err=>{
	        	console.log(err)
	        })
        },
        goBack(){
        	this.$router.go(-1);
        }
        
    }
}


</script>
<style scoped>
li {
    list-style: none;
}

ul {
    margin: 0;
    padding: 0;
}

.photo-title {
    overflow: hidden;
}

.photo-title,
.photo-desc {
    border-bottom: 1px solid rgba(0, 0, 0, 0.2);
    padding-bottom: 5px;
    margin-bottom: 5px;
    padding-left: 5px;
}

.photo-title p {
    color: #13c2f7;
    font-size: 20px;
    font-weight: bold;
}

.photo-title span {
    margin-right: 20px;
}

.mui-table-view.mui-grid-view.mui-grid-9 {
    background-color: white;
    border: 0;
}

.mui-table-view.mui-grid-view.mui-grid-9 li {
    border: 0;
}

.photo-desc p {
    font-size: 18px;
}

.mui-table-view-cell.mui-media.mui-col-xs-4.mui-col-sm-3 {
    padding: 2 2;
}

/*评论样式 开始*/
.photo-comment > div span:nth-child(1) {
    float: left;
    font-weight: bold;
    margin-left: 5px;
}

.photo-comment > div span:nth-child(2) {
    float: right;
}

.photo-comment {
    height: 30px;
    border-bottom: 1px solid rgba(0, 0, 0, 0.4);
    line-height: 30px;
    margin-bottom: 5px;
}

.txt-comment {
    padding: 5 5;
}

.txt-comment textarea {
    margin-bottom: 5px;
}

.comment-list li {
    border-bottom: 1px solid rgba(0, 0, 0, 0.2);
    padding-bottom: 5px;
    margin-bottom: 5px;
    padding-left: 5px;
}

li {
    list-style: none;
}

ul {
    margin: 0;
    padding: 0;
}

/*评论样式 结束*/

</style>
