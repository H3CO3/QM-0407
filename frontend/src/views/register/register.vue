<template>
    <div class="register">
        <h1>青芒</h1>
        <el-tooltip class="item" effect="dark" content="输入3到15位字母、数字、下划线组合" placement="top-start">
            <el-input placeholder="请输入用户名" v-model="id" maxlength="15"    prefix-icon="el-icon-user-solid" clearable></el-input>
        </el-tooltip>
        <span  v-if="idRemind" id="idRemind">{{idTips}}</span>
        <br/>
        <el-tooltip class="item" effect="dark" content="输入8到16位字符组合" placement="top-start">
            <el-input  placeholder="请输入密码"  v-model="password" maxlength="16" prefix-icon="el-icon-lock" show-password></el-input>
        </el-tooltip>
        <span  v-if="passwordRemind" id="passwordRemind">{{passwordTips}}</span>
        <br/>
        <el-input  placeholder="请再次输入密码"  v-model="password1" maxlength="16" prefix-icon="el-icon-lock" show-password></el-input>
        <span  v-if="passwordConfirm" id="passwordConfirm">{{confirmTips}}</span>
        <br/>
        <el-tooltip class="item" effect="dark" content="输入1到16位中英字符组合" placement="top-start">
            <el-input  placeholder="请输入昵称"  v-model="nickname" maxlength="16" prefix-icon="el-icon-edit-outline" clearable></el-input>
        </el-tooltip>
        <span  v-if="nicknameRemind" id="nicknameRemind">{{nicknameTips}}</span>
        <br/>
        <el-button id="register-submit" @click="register" :loading="isLoading">{{registerText}}</el-button>
        <br/>
        <el-button id="login"  @click="login" type="text">登录</el-button>
    </div>
</template>

<script>
    const usernamePat = /^[a-zA-Z0-9_-]{3,15}$/;
    export default {
        name: "register",
        data(){
            return{
                id: "",
                password: "",
                password1:"",
                nickname:"",
                registerText:"注册",
                idRemind:false,
                passwordRemind:false,
                passwordConfirm:false,
                nicknameRemind:false,
                isLoading:false
            }
        },
        methods:{
            register(){
                if(this.id == "")
                {
                    this.$message({message:"账号不能为空",type:"warning"});
                }
                else if(!usernamePat.test(this.id)){
                    this.$message({message:"账号不合法",type:"warning"});
                }
                else if(this.password == "")
                {
                    this.$message({message:"密码不能为空",type:"warning"});
                }
                else if(this.password.length < 8){
                    this.$message({message:"密码过短",type:"warning"});
                }
                else if(this.passwordConfirm == true)
                {
                    this.$message({message:"两次密码输入不一致", type:"warning"});
                }
                else if(this.nickname == "")
                {
                    this.$message({message:"昵称不能为空", type:"warning"});
                }
                else{
                    this.isLoading = true;
                    this.registerText = "提交注册中...";
                    this.$axios.post("register",  {
                        username: this.id,
                        password: this.password,
                        nickname: this.nickname
                    }).then((result)=>{
                        console.log(result);
                        this.$message({message:"注册成功",type:"success"});
                        window.location.href = "login";
                    }).catch((error)=>{
                        console.log(error.response.status);
                        if(error.response.status == 400)
                            this.$message({message:"注册失败，该账号已被使用",type:"error"});
                        else if(error.response.status == 403)
                            this.$message({message:"您已登录，不可注册",type:"warning"});
                        else
                            this.$message({message:"服务器未响应",type:"warning"});
                        this.isLoading = false;
                        this.registerText = "注册";
                    })
                }
            },
            login(){
                window.location.href = "login";
            }

        },
        watch:{
            id(val){
              if(val == "")
              {
                  this.idTips = "账号不能为空";
                  this.idRemind = true;

              }
              else if(!usernamePat.test(val))
              {
                  this.idTips = "账号不合法";
                  this.idRemind = true;
              }
              else{
                  this.idRemind = false;
              }
            },
            password(val){
                if(val == "") {
                    this.passwordTips = "密码不能为空";
                    this.passwordRemind = true;
                }
                else if(val.length < 8){
                    this.passwordTips = "密码过短";
                    this.passwordRemind = true;
                }
                else{
                    this.passwordRemind = false;
                }
                if(this.password1!="") {
                    if(val != this.password1)
                    {
                        this.confirmTips = "两次密码输入不一致";
                        this.passwordConfirm = true;
                    }
                    else{
                        this.passwordConfirm = false;
                    }
                }
            },
            password1(val){
                if(val != this.password)
                {
                    this.confirmTips = "两次密码输入不一致";
                    this.passwordConfirm = true;
                }
                else{
                    this.passwordConfirm = false;
                }
            },
            nickname(val){
                if(val == "")
                {
                    this.nicknameTips = "昵称不能为空";
                    this.nicknameRemind = true;
                }
                else
                {
                    this.nicknameRemind = false;
                }
            }
        }

    };
</script>
<style  lang="css" scoped>
    .register >>>.el-input{
        width: 440px;
        height: 60px;
        font-size: 25px;
        margin-top: 35px;
    }
    .el-input >>>.el-input__inner{
        padding-left: 100px !important;
        width: 440px;
        height: 60px;
        line-height: 20px;
        opacity: 0.5;
        border-radius: 30px;;
        border: 1px solid rgba(255, 255, 255, 1);
        letter-spacing: 3px;
    }
    [class^=el-icon-], >>> [class*=" el-icon-"]{
        display:contents !important;
        font-size: 29px !important;
        color: rgb(255, 255, 255) !important;
    }
    .el-input >>>.el-input__prefix{
        left:40px !important;
    }
    .el-input >>>.el-input__suffix{
        right: 40px !important;
    }
    #register-submit{
        margin-top: 35px;
        width: 440px;
        height: 60px;
        line-height: 29px;
        border-radius: 30px;
        background-color: rgba(58, 98, 215, 1);
        color: rgba(255, 255, 255, 1);
        font-size: 20px;
        text-align: center;
        font-family: Roboto;
        border: 1px solid rgba(58, 98, 215, 1);
    }
    #register-submit:hover{
        background-color: rgb(41, 48, 215);
    }
    #login{
        margin-top: 18px;
        position: relative;
        left:140px;
        width: 158px;
        height: 56px;
        line-height: 29px;
        opacity: 0.65;
        background-color: rgba(255, 255, 255, 0);
        color: rgba(227, 225, 225, 1);
        font-size: 20px;
        text-align: center;
        font-family: Roboto;
        border: 1px solid rgba(255, 255, 255, 0);

    }
    #login:hover{
        opacity: 1;
        color: rgb(255, 253, 253);
    }
    #idRemind,#passwordRemind,#passwordConfirm,#nicknameRemind{
        position: absolute;
        margin-top: 60px;
        margin-left: 30px;
        font-size: 18px;
        color: #ff0000c2;
    }
    .register{
        width:50%;
        padding: 20px;
        margin: 0 auto;
    }


</style>
