<template>
  <div class="edit">
    <!-- <el-row>
      <el-col :span="24"><div class="grid-content bg-purple-dark"></div></el-col>
    </el-row> -->

    <el-form ref="form" :model="forminfo" label-width="80px">

      <el-form-item label="加密">
        <el-select v-model="forminfo.method" placeholder="请选择加密方式（method）">
          <el-option label="none" value="none"></el-option>
          <el-option label="aes-128-cfb" value="aes-128-cfb"></el-option>
          <el-option label="aes-192-cfb" value="aes-192-cfb"></el-option>
          <el-option label="aes-256-cfb" value="aes-256-cfb"></el-option>
          <el-option label="rc4-md5" value="rc4-md5"></el-option>
          <el-option label="rc4-md5-6" value="rc4-md5-6"></el-option>
          <el-option label="chacha20" value="chacha20"></el-option>
          <el-option label="chacha20-ietf" value="chacha20-ietf"></el-option>
          <el-option label="salsa20" value="salsa20"></el-option>
          <el-option label="aes-128-ctr" value="aes-128-ctr"></el-option>
          <el-option label="aes-192-ctr" value="aes-192-ctr"></el-option>
          <el-option label="aes-256-ctr" value="aes-256-ctr"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="协议">
        <el-select v-model="forminfo.protocol" placeholder="请选择协议（protocol）">
          <el-option label="origin" value="origin"></el-option>
          <el-option label="auth_sha1_v4" value="auth_sha1_v4"></el-option>
          <el-option label="auth_sha1_v4_compatible" value="auth_sha1_v4_compatible"></el-option>
          <el-option label="auth_aes128_md5" value="auth_aes128_md5"></el-option>
          <el-option label="auth_aes128_sha1" value="auth_aes128_sha1"></el-option>
          <el-option label="auth_chain_a" value="auth_chain_a"></el-option>
        </el-select>
      </el-form-item>


      <el-form-item label="混淆">
        <el-select v-model="forminfo.obfs" placeholder="请选择混淆方式（OBFS）">
          <el-option label="plain" value="plain"></el-option>
          <el-option label="http_simple_compatible" value="http_simple_compatible"></el-option>
          <el-option label="http_simple" value="http_simple"></el-option>
          <el-option label="tls1.2_ticket_auth_compatible" value="tls1.2_ticket_auth_compatible"></el-option>
          <el-option label="tls1.2_ticket_auth" value="tls1.2_ticket_auth"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="e-mail">
        <el-input v-model="forminfo.email" placeholder="新邮箱地址"></el-input>
      </el-form-item>

      <el-form-item label="ss密码">
        <el-input v-model="forminfo.ss_password" placeholder="新ss密码 建议与登录密码不同"></el-input>
      </el-form-item>

      <el-form-item label="密码">
        <el-input v-model="forminfo.password" placeholder="新登录密码 建议与ss密码不同"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="onSubmit">提交</el-button>
        <el-button @click="$router.push('/view')">取消</el-button>
      </el-form-item>
    </el-form>


  </div>
</template>

<script>
  export default {
    name: 'edit',
    data () {
      return {
        forminfo: {
          email: '',
          ss_password:'',
          password:'',
          method: '',
          protocol: '',
          obfs: '',
        },
        userinfo: {
          email: '',
          ss_password:'',
          password:'',
          method: '',
          protocol: '',
          obfs: '',
        }
      }
    },
    methods: {
      onSubmit() {
        this.$confirm('此操作将修改您的Shadowsocks配置, 是否继续?', '提示', {
          confirmButtonText: "提交",
          cancelButtonText: "取消",
          type: "warning"
        })
        .then(() => {
          for (var k in this.forminfo){
            if(this.forminfo[k]=='' || this.forminfo[k]==this.userinfo[k]){
              delete this.forminfo[k]
            }
          }
          if(Object.keys(this.forminfo).length === 0 && this.forminfo.constructor === Object){
            this.$message.error('修改后的信息与原来一致!')
          } else {
            console.log(this.forminfo)
            this.$ajax.patch('/users/'.concat(sessionStorage.getItem('username')), this.forminfo)
            .then((response) => {
              if(response.data.Success){
                this.$message.success('提交成功! 请自行设置本地客户端配置')
                this.$router.push('/view')
              }
            })
            .catch((error) => {
              console.log(error)
            })
          }
        })
        .catch(()=>{})
      }
    },
    created(){
      if(!(sessionStorage.getItem("state") && JSON.parse(sessionStorage.getItem("state")))){
        this.$router.push('/login')
      } else {
        this.$ajax.get('/users/'.concat(sessionStorage.getItem("username")))
        .then((response) => {
          this.userinfo = response.data
          this.forminfo = JSON.parse(JSON.stringify(this.userinfo))
        })
        .catch((error) =>{
          console.log(error)
        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .el-form{
    border: 1px solid #eaeefb;
    border-radius: 4px;
    padding: 24px;
    width: 360px;
    margin: auto;
  }
  .el-select{
    width: 250px;
  }
  .el-input{
    width: 250px;
  }
</style>
