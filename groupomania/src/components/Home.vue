<template>  
  <el-container class="home-container">
    <!-- head area -->
    <el-header>
      <div>
        <img src="../assets/icon-left-font-monochrome-white.svg" alt="">
        <span>User: {{userinfo}}</span>        
      </div>
      <div>
        <el-button type="primary" @click="postNew">New Post</el-button>
        <el-button type="primary" @click="profile">Profile</el-button>
        <el-button type="info" @click="logout">Logout</el-button>
      </div>
      
    </el-header>    
    <!-- main area -->
    <el-main>
      <el-tabs v-model="activeName">
      <el-tab-pane label="All posts" name="first">
      <div class="articles">
      <article v-for="item in reversePost" :key="item.id">
      <h3 @click="articleDetail(item._id)">{{ item.name }}</h3>
        <div class="info-box">
        <!--    title      -->
        <p v-html="formatWord(item.description).substring(0,50)+'...'"></p>
        <ul>
          <!--    author        -->
          <li class="author">            
            <span>Author: {{ item.manufacturer }}</span>
          </li>
          <!--    create time        -->
          <li class="createTime">          
            <span>Post at: {{ item.createdAt | dateFormat }}</span>
          </li>
        </ul>
        </div>
      </article>
      </div>
      </el-tab-pane>
      <el-tab-pane label="Unread posts" name="second">     
      <div class="articles">
      <article v-for="item in reverseUnReadPost" :key="item.id">
      <h3 @click="articleDetail(item._id)">{{ item.name }}</h3>
        <div class="info-box">
        <!--    title      -->
        <p v-html="formatWord(item.description).substring(0,50)+'...'"></p>
        <ul>
          <!--    author        -->
          <li class="author">            
            <span>Author: {{ item.manufacturer }}</span>
          </li>
          <!--    create time        -->
          <li class="createTime">          
            <span>Post at: {{ item.createdAt | dateFormat }}</span>
          </li>
        </ul>
        </div>
      </article>
      </div>      
      </el-tab-pane>
      </el-tabs>
    </el-main>
    <!-- footer area -->
    <el-footer>Footer</el-footer>    
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      userinfo: sessionStorage.username,
      menulist: [],
      unreadmenulist:[], 
      activeName: 'second',
      brief:[],     
    }
  },

  created() {
    this.getMenuList()
    this.getUnreadPost()
  },

  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    profile() {      
      this.$router.push('/profile')
    },
    postNew() {      
      this.$router.push('/newpost')
    },
    // get all post 
    async getMenuList() {
      const {data: res, status: ress} = await this.$http.get('sauces');
      if (ress !== 200) return this.$message.error('can not read posts');        
      this.menulist = res;      
      //console.log(this.menulist);
    },

    // get unread post 
    async getUnreadPost() {
      const {data: res, status: ress} = await this.$http.get('postread/'+sessionStorage.userId);
      if (ress !== 200) return this.$message.error('can not read posts');        
      this.unreadmenulist = res;        
      }, 
    

    // go to article detail page
    articleDetail(articleid) {
      this.$router.push({
          path: '/article',
          query: {id: articleid}
        })
    },   

  },

  computed: {
    //Sort posts in reverse chronological order
    reversePost() {
      return this.menulist.reverse();
    },
    reverseUnReadPost() {
      return this.unreadmenulist.reverse();
    },
  },

  filters: {
    // format time string
    dateFormat(time) {
      let dt = new Date(time);
      const y = dt.getFullYear();
      const m = (dt.getMonth() + 1 + "").padStart(2, "0");
      const d = (dt.getDate() + "").padStart(2, "0");
      const hh = (dt.getHours() + "").padStart(2, "0");
      const mm = (dt.getMinutes() + "").padStart(2, "0");
      const ss = (dt.getSeconds() + "").padStart(2, "0");
      return `${y}.${m}.${d} ${hh}:${mm}:${ss}`;
    },
  },
};


</script>

<style lang="less" scoped>
@import '../assets/css/header.less';
@import '../assets/css/articles.less';

h3 {
      &:hover {
            color: blue;
            cursor: pointer;
        }        
      }

.el-tabs {
  padding-left: 15%;
  width: 70%;
}

</style>