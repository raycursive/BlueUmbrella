<template>
  <div class="logout" @keyup.enter="logout">
    <!-- <el-row>
      <el-col :span="24"><div class="grid-content bg-purple-dark"></div></el-col>
    </el-row> -->
    <el-card class="box-card">
      <!-- Navigate Row  -->
      <el-row>
        <el-col :span="12" :offset="2">
          <div id="content">当前用户是: {{ current_user }}</div>
        </el-col>
        <el-col :span="2" :offset="4">
          <div id="content"><el-button @click="logout" type="primary">登出</el-button></div>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'logout',
  computed: {
    current_user(){
      return sessionStorage.getItem("username")
    }
  },
  methods: {
    logout(){
      this.$ajax.get('/logout')
      .then( (response) => {
        sessionStorage.clear()
        this.$router.push('/login')
      })
    }
  },
  created(){
    if(!(sessionStorage.getItem("state") && JSON.parse(sessionStorage.getItem("state")))){
      this.$router.push('/login')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .el-row {
    margin-bottom: 20px;
  }
  .el-row:last-child {
      margin-bottom: 0;
  }
  .el-col {
    border-radius: 4px;
  }
  .box-card {
    margin: auto;
    width: 50%;
  }
  #content {
    height: 50px;
    line-height: 50px;
  } 
  </style>
