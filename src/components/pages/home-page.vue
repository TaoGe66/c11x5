<template>
  <section class="home">
    <!--左侧操作-->
    <div class="left">
      <div v-title>11选5主页面</div>
      <header class="header">
        距离<span v-text="timeInfo.pid"></span>期投注截止时间还剩<span v-text="content"></span>
      </header>
      <div class="type_select">
        <ul class="select_btn">
          <li v-for="(item,index) in methodType" @click="type(index,item)" class="method_type" :class="{classname: index==qwerqwre}">{{item.name}}</li>
        </ul>
      </div>
      <div class="tips" v-model="tipsContent">{{tipsContent}}</div>

      <!--选号区-->
      <div class="box" v-show="normal">
        <ul class="head">
          <li v-for="(item,index) in firstRow" @click="chooseRed(index,item)" :style="{background:redBall.indexOf(index)==-1?'white':'red',color:redBall.indexOf(index)==-1?'red':'white'}" >{{item}}</li>
        </ul>
        <ul class="head" v-show="twoZhi">
          <li v-for="(item,index) in twoRow" @click="twoChooseRed(index,item)" :style="{background:twoRedBall.indexOf(index)==-1?'white':'red',color:twoRedBall.indexOf(index)==-1?'red':'white'}" >{{item}}</li>
        </ul>
        <ul class="head" v-show="threeZhi">
          <li v-for="(item,index) in threeRow" @click="threeChooseRed(index,item)" :style="{background:threeRedBall.indexOf(index)==-1?'white':'red',color:threeRedBall.indexOf(index)==-1?'red':'white'}" >{{item}}</li>
        </ul>
        <p v-show="multipleNumber" class="betting_demo">您选了<span> {{betsNum}} </span>注，共<span> {{betsNum*2*times}} </span>元</p>
        <p v-show="multipleNumber2" class="betting_demo">您选了<span> {{qrzx}} </span>注，共<span> {{betsNum2*2*times}} </span>元</p>
      </div>

      <div class="box" v-show="dancingDrag">
        <p class="dan1">胆</p>
        <ul class="head">
          <li v-for="(item,index) in firstRow" @click="chooseRed(index)" :style="{background:redBall.indexOf(index)==-1?'#fff':'red'}" >{{item}}</li>
        </ul>
        <p class="tuo2">拖</p>
        <ul class="head">
          <li v-for="(item,index) in firstRow" @click="chooseRed(index)" :style="{background:redBall.indexOf(index)==-1?'#fff':'red'}" >{{item}}</li>
        </ul>
        <p class="betting_demo">您选了<span> {{betsNum}} </span>注，共<span> {{betsNum*2*times}} </span>元</p>
      </div>

      <!--票池-->
      <div class="show_select">
        <el-table
          :data="tableData"
          style="width: 100%"
          height="243">
          <el-table-column type="index" width="100" label="序号" header-align="center" align="center">
          </el-table-column>
          <el-table-column prop="playName" label="玩法" width="150" header-align="center" align="center">
          </el-table-column>
          <el-table-column prop="playType" label="投注方式" width="150" header-align="center" align="center">
            <template slot-scope="scope">
              {{scope.row.playType === 1 ? "单选" : (scope.row.playType === 2 ? "复式" : "胆拖")}}
            </template>
          </el-table-column>
          <el-table-column prop="code" label="号码" header-align="center" align="center">
            <template slot-scope="scope">
              {{scope.row.code | numberBall}}
            </template>
          </el-table-column>
          <el-table-column prop="multiple" label="倍数" width="150" header-align="center" align="center">
          </el-table-column>
          <el-table-column prop="money" label="金额" width="150" header-align="center" align="center">
          </el-table-column>
          <el-table-column label="操作" width="250" header-align="center" align="center">
            <template slot-scope="scope">
              <el-button @click="deleteOne(scope.row,scope.$index)" type="text" size="medium">删除</el-button>
              <el-button type="text" size="medium">编辑</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </div>

    <!--右侧操作-->
    <div class="right" v-show="normal">
      <span class="login" v-show="login" @click="loginShow">登录</span><span class="out" v-show="out" @click="loginOut">退出</span>
      <p class="history">历史开奖</p>
      <router-link to="/order"><p class="my_order">我的订单</p></router-link>

      <p class="multiple_input">倍数</p>
      <div>
        <span class="reduce" @click="reduce()">－</span><input type="text" class="times" v-model="times"><span class="add" @click="add()" >＋</span>
      </div>
      <p class="max_add" @click="maxAdd()">99倍</p>
      <!--<p class="dancing_drag" @click="showDanTuo">胆拖</p>-->
      <p class="random_lottery" @click="Rondom1" v-show="rondom1">机选</p>
      <p class="random_lottery" @click="Rondom2" v-show="rondom2">机选</p>
      <p class="random_lottery" @click="Rondom3" v-show="rondom3">机选</p>
      <p class="add_lottery" @click="addLottery">添加</p>
    </div>

    <div class="right" v-show="dancingDrag">
      <span class="login" v-show="login" @click="loginShow">登录</span><span class="out" v-show="out" @click="loginOut">退出</span>
      <p class="history">历史开奖</p>
      <p class="my_order">我的订单</p>
      <p class="multiple_input">倍数</p>
      <div>
        <span class="reduce" @click="reduce()">－</span><input type="text" class="times" v-model="times"><span class="add" @click="add()" >＋</span>
      </div>
      <p class="save" @click="saveDanTuo">保存</p>
      <p class="quit" @click="quit">退出</p>
    </div>

    <!--底部-->
    <footer>
      <p class="footer_text">共计金额：<span>{{totalPrice}} 元</span></p><span class="sub_btn" @click="subAll">投注</span><button @click="deleteAll" class="delete_all">清空票池</button>
    </footer>

    <!--登录弹窗-->
    <div class="login-wrap" v-show="model">
      <div class="bg_model">
        <div class="ms-login">
          <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="0px" class="demo-ruleForm">
            <el-form-item prop="username">
              <input type="text" v-model="ruleForm.username" placeholder="用户名" class="ms-input">
            </el-form-item>
            <el-form-item prop="password">
              <input type="password" placeholder="密码" v-model="ruleForm.password" class="ms-input">
            </el-form-item>
            <div class="login-btn">
              <el-button type="primary" @click="submitForm()">登录</el-button>
            </div>
          </el-form>
        </div>
      </div>
    </div>

  </section>
</template>

<script>
  export default{
    data(){
      return{
        login: true,
        out: false,
        model: false,
        ruleForm: {
          username: '',
          password: ''
        },
        rules: {
          username: [
            { required: true, message: '请输入用户名', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请输入密码', trigger: 'blur' }
          ]
        },
        startInfo:[],
        timeInfo:{
          pid:'',
          bt:'',
          et:'',
          at:'',
        },
        methodType:[{
          name:'任五',
          tips:'从01～11中任选5个或多个号码，所选号码与开奖号码全部相同，即中奖540元。'
        },{
          name:'任一',
          tips:'从01～11中任选1个或多个号码，所选号码与开奖号码第一位号码相同，即中奖13元。'
        },{
          name:'任二',
          tips:'从01～11中任选2个或多个号码，所选号码与开奖号码任意两个号码相同，即中奖6元。'
        },{
          name:'任三',
          tips:'从01～11中任选3个或多个号码，所选号码与开奖号码任意三个号码相同，即中奖19元。'
        },{
          name:'任四',
          tips:'从01～11中任选4个或多个号码，所选号码与开奖号码任意四个号码相同，即中奖78元。'
        },{
          name:'任六',
          tips:'从01～11中任选6个或多个号码，所选号码与开奖号码五个号码相同，即中奖90元。'
        },{
          name:'任七',
          tips:'从01～11中任选7个或多个号码，所选号码与开奖号码五个号码相同，即中奖26元。'
        },{
          name:'任八',
          tips:'从01～11中任选8个或多个号码，所选号码与开奖号码五个号码相同，即中奖9元。'
        },{
          name:'前二直选',
          tips:'从万、千位各选1个或多个号码，所选号码与开奖号码前两位号码相同（且顺序一致），即中奖130元。'
        },{
          name:'前三直选',
          tips:'从万、千、百位各选1或多个号码，选号与开奖号码前三位相同（且顺序一致），即中1170元。'
        },{
          name:'前二组选',
          tips:'从01～11中任选2个或多个号码，所选号码与开奖号码前两位号码相同（顺序不限），即中奖65元。'
        },{
          name:'前三组选',
          tips:'从01～11中选3或多个号码，选号与开奖号码前三位相同（顺序不限），即中奖195元。'
        }],
        tipsContent:'',
        firstRow:['01','02','03','04','05','06','07','08','09','10','11'],
        twoRow:['01','02','03','04','05','06','07','08','09','10','11'],
        threeRow:['01','02','03','04','05','06','07','08','09','10','11'],
        tableData: [],
        dancingDrag:false,
        normal:true,
        twoZhi:false,
        threeZhi:false,
        qwerqwre: "0",
        methodNum: 5,//默认任五
        redBall:[],
        redBall2:[],
        twoRedBall:[],
        twoRedBall2:[],
        threeRedBall:[],
        threeRedBall2:[],
        aaa:'',
        bbb:'',
        ccc:'',
        method:'',
        tableDemo:{
          method:'',
          mode:'',
          number:'',
          multiple:'',
          price:'',
          methodNumber: 5,
          code: 5,//默认任五
        },
        betsNum:0,
        betsNum2:0,
        qrzx:0,
        multipleNumber:true,
        multipleNumber2:false,
        rondom1:true,
        rondom2:false,
        rondom3:false,
        times:1,
        row:'',
        index:'',
        minutes:10,
        seconds:0,
        totalPrice:0,
        loginInfo:'',
        content:'',
      }
    },
    /*watch:{
      //开奖倒计时
      second:{
        handler(newVal){
          this.num(newVal)
        }
      },
      minute:{
        handler(newVal){
          this.num(newVal)
        }
      }
    },*/
    created(){
      this.tipsContent = this.methodType[0].tips;
      this.tableDemo.method = this.methodType[0].name;
      this.tableDemo.multiple = this.times;
//      localStorage.removeItem('userId');
      this.query();
    },
    mounted(){
    },
    /*computed:{
      //开奖倒计时
      second:function () {
        return this.num(this.seconds)
      },
      minute:function () {
        return this.num(this.minutes)
      },

    },*/
    methods:{
      //查询期号和开奖时间
      query(){
        console.log("________________ffffdf_________________");
        let _this = this;
        let params = {};
        _this.startInfo = [];
//        _this.timeInfo.pid = '';
        _this.timeInfo.et = '';
        _this.$http.post('/getElevenselectfiveInfo',params).then((res)=>{
          _this.startInfo = res.data;
          _this.timeInfo.pid = _this.startInfo.info.pid;
          _this.timeInfo.et = _this.startInfo.info.et;
          _this.addTime();
        },(err)=>{
          console.log(err);
        })
      },
      submitForm() {
        let _this = this;
        let params = {
          username:_this.ruleForm.username,
          password:_this.ruleForm.password,
        };
        this.$http.post('/userLogin',params).then((res)=>{
          _this.loginInfo = res.data;
          console.log(_this.loginInfo.userId);
          if(_this.loginInfo.code==='0'){
            localStorage.setItem('userId',_this.loginInfo.userId);
            _this.login = false;
            _this.out = true;
            _this.model = false;
          }else {
            _this.open6();
            _this.ruleForm.username = '';
            _this.ruleForm.password = '';
            _this.login = true;
            _this.out = false;
            _this.model = true;
          }
        });
      },
      //登录失败
      open6() {
        this.$message.error({
          showClose: true,
          message: '用户名或密码错误，请重新输入',
        });
      },
      open7() {
        this.$message({
          showClose: true,
          message: '您已退出登录',
          type: 'warning'
        });
      },
      loginShow(){
        this.model = true;
      },
      loginOut(){
        this.login = true;
        this.out = false;
        this.open7();
        localStorage.removeItem('userId');
      },

      //胆拖页面切换
      showDanTuo(){
        this.dancingDrag = true;
        this.normal = false;
      },
      saveDanTuo(){
        this.dancingDrag = false;
        this.normal = true;
      },
      quit(){
        this.dancingDrag = false;
        this.normal = true;
      },

      //开奖倒计时
      num:function (n) {
        return n<10 ? "0" + n : "" + n
      },
      addTime:function () {
        let _this = this;
        let timer = setInterval(function(){
          let nowTime = new Date();
          let endTime = new Date((_this.timeInfo.et));
//          console.log(endTime.getTime());
//          console.log(nowTime.getTime());
          let t = endTime.getTime() - (nowTime.getTime());
//          console.log(t);
          if(t>0){
            let min=Math.floor((t/60000)%60);
            let sec=Math.floor((t/1000)%60);
            min = min < 10 ? "0" + min : min;
            sec = sec < 10 ? "0" + sec : sec;
            let format = '';
            if(min > 0 && sec > 0){
              format =` ${min} : ${sec}`;
            }
            if(min <= 0){
              format =` 00 : ${sec}`;
            }
            if(sec <= 0){
              format =` ${min} : ${sec}`;
            }
            _this.content = format;
          }else{
            clearInterval(timer);
//            console.log('清除定时器');
//            console.log(t);
//            console.log(endTime);
            _this.query();
          }
        },1000);
      },
      //玩法类型选择
      type(index,item){
        this.qwerqwre = index;
        this.tipsContent = item.tips;
        this.tableDemo.method = item.name;
        this.redBall = [];
        this.redBall2 = [];
        this.twoRedBall = [];
        this.twoRedBall2 = [];
        this.threeRedBall = [];
        this.threeRedBall2 = [];
        this.betsNum = 0;
        this.betsNum2 = 0;
        if(item.name === '前二直选'){
          this.twoZhi = true;
          this.threeZhi = false;
          this.multipleNumber = false;
          this.multipleNumber2 = true;
          this.rondom1 = false;
          this.rondom2 = true;
          this.rondom3 = false;
        }
        if(item.name === '前三直选'){
          this.twoZhi = true;
          this.threeZhi = true;
          this.multipleNumber = false;
          this.multipleNumber2 = true;
          this.rondom1 = false;
          this.rondom2 = false;
          this.rondom3 = true;
        }
        if(item.name !== '前二直选'&&item.name !== '前三直选'){
          this.twoZhi = false;
          this.threeZhi = false;
          this.multipleNumber = true;
          this.multipleNumber2 = false;
          this.rondom1 = true;
          this.rondom2 = false;
          this.rondom3 = false;
        }
        if(item.name === '任一'){
          this.methodNum = 1;
          this.tableDemo.methodNumber = 1;
        }
        if(item.name === '任二'||item.name === '前二组选'){
          this.methodNum = 2;
          this.tableDemo.methodNumber = 2;
        }
        if(item.name === '任三'||item.name === '前三组选'){
          this.methodNum = 3;
          this.tableDemo.methodNumber = 3;
        }
        if(item.name === '任四'){
          this.methodNum = 4;
          this.tableDemo.methodNumber = 4;
        }
        if(item.name === '任五'){
          this.methodNum = 5;
          this.tableDemo.methodNumber = 5;
        }
        if(item.name === '任六'){
          this.methodNum = 6;
          this.tableDemo.methodNumber = 6;
        }
        if(item.name === '任七'){
          this.methodNum = 7;
          this.tableDemo.methodNumber = 7;
        }
        if(item.name === '任八'){
          this.methodNum = 8;
          this.tableDemo.methodNumber = 8;
        }
        if(item.name === '前二直选'){
          this.tableDemo.methodNumber = 9;
        }
        if(item.name === '前三直选'){
          this.tableDemo.methodNumber = 10;
        }
        if(item.name === '前二组选'){
          this.tableDemo.methodNumber = 11;
        }
        if(item.name === '前三组选'){
          this.tableDemo.methodNumber = 12;
        }
      },
      //删除票池点击当前
      deleteOne(row,index) {
        this.row = row;
        this.index = index;
        console.log(this.row);
        console.log(index);
        this.tableData.splice(index,1);
        this.totalPrice -= this.row.money;
      },
      //清空票池所有
      deleteAll(){
        this.tableData.splice(0,this.tableData.length);
        this.totalPrice = 0;
      },
      // 点击第一行红球
      chooseRed(index,item){
        if(this.redBall.indexOf(index)==-1){
          this.redBall.push(index);
          this.redBall2.push(item);
          // 第二行排除
          if(this.twoRedBall2.indexOf(item)==-1){
          }else {
            this.twoRedBall.splice(this.twoRedBall.indexOf(index),1);
            this.twoRedBall2.splice(this.twoRedBall2.indexOf(item),1);
          }
          // 第三行排除
          if(this.threeRedBall2.indexOf(item)==-1){
          }else {
            this.threeRedBall.splice(this.threeRedBall.indexOf(index),1);
            this.threeRedBall2.splice(this.threeRedBall2.indexOf(item),1);
          }
        // 第一行删除
        }else{
          this.redBall.splice(this.redBall.indexOf(index),1);
          this.redBall2.splice(this.redBall2.indexOf(item),1);
        }
        this.aaa = this.redBall2.length;
        this.bbb = this.twoRedBall2.length;
        this.ccc = this.threeRedBall2.length;
        if(this.tableDemo.method === '前二直选'){
          this.betsNum2 = this.aaa*this.bbb;
        }
        if(this.tableDemo.method === '前三直选'){
          this.betsNum2 = this.aaa*this.bbb*this.ccc;
        }
      },
      // 点击第二行红球
      twoChooseRed(index,item){
        if(this.twoRedBall.indexOf(index)==-1){
          this.twoRedBall.push(index);
          this.twoRedBall2.push(item);
          // 第一行排除
          if(this.redBall2.indexOf(item)==-1){
          }else {
            this.redBall.splice(this.redBall.indexOf(index),1);
            this.redBall2.splice(this.redBall2.indexOf(item),1);
          }
          // 第三行排除
          if(this.threeRedBall2.indexOf(item)==-1){
          }else {
            this.threeRedBall.splice(this.threeRedBall.indexOf(index),1);
            this.threeRedBall2.splice(this.threeRedBall2.indexOf(item),1);
          }
        // 第二行删除
        }else {
          this.twoRedBall.splice(this.twoRedBall.indexOf(index),1);
          this.twoRedBall2.splice(this.twoRedBall2.indexOf(item),1);
        }
        this.aaa = this.redBall2.length;
        this.bbb = this.twoRedBall2.length;
        this.ccc = this.threeRedBall2.length;
        if(this.tableDemo.method === '前二直选'){
          this.betsNum2 = this.aaa*this.bbb;
        }
        if(this.tableDemo.method === '前三直选'){
          this.betsNum2 = this.aaa*this.bbb*this.ccc;
        }
      },
      // 点击第三行红球
      threeChooseRed(index,item){
        if(this.threeRedBall.indexOf(index)==-1){
          this.threeRedBall.push(index);
          this.threeRedBall2.push(item);
          // 第一行排除
          if(this.redBall2.indexOf(item)==-1){
          }else {
            this.redBall.splice(this.redBall.indexOf(index),1);
            this.redBall2.splice(this.redBall2.indexOf(item),1);
          }
          // 第二行排除
          if(this.twoRedBall2.indexOf(item)==-1){
          }else {
            this.twoRedBall.splice(this.twoRedBall.indexOf(index),1);
            this.twoRedBall2.splice(this.twoRedBall2.indexOf(item),1);
          }
        // 第三行删除
        }else{
          this.threeRedBall.splice(this.threeRedBall.indexOf(index),1);
          this.threeRedBall2.splice(this.threeRedBall2.indexOf(item),1);
        }
        this.aaa = this.redBall2.length;
        this.bbb = this.twoRedBall2.length;
        this.ccc = this.threeRedBall2.length;
        if(this.tableDemo.method === '前二直选'){
          this.betsNum2 = this.aaa*this.bbb;
        }
        if(this.tableDemo.method === '前三直选'){
          this.betsNum2 = this.aaa*this.bbb*this.ccc;
        }
      },

      // 随机方法
      rondomRedBall(){
        this.redBall2 = [];
        this.redBall = new Array(11)
          .fill(0)
          .map((v,i)=>i)
          .sort(()=>0.5 - Math.random())
          .filter((v,i)=>i<1);
      },
      rondomTwoRedBall(){
        this.twoRedBall2 = [];
        this.twoRedBall = new Array(11)
          .fill(0)
          .map((v,i)=>i)
          .sort(()=>0.5 - Math.random())
          .filter((v,i)=>i<1);
      },
      rondomThreeRedBall(){
        this.threeRedBall2 = [];
        this.threeRedBall = new Array(11)
          .fill(0)
          .map((v,i)=>i)
          .sort(()=>0.5 - Math.random())
          .filter((v,i)=>i<1);
      },
      // 随机投注方式
      Rondom1(){
        let rondomNum = [];
        this.redBall2 = [];
        this.redBall = new Array(11)
          .fill(0)
          .map((v,i)=>i)
          .sort(()=>0.5 - Math.random())
          .filter((v,i)=>i<this.methodNum);
        for(var i=0;i<this.redBall.length;i++){
          rondomNum = this.num(this.redBall[i]+1);
          this.redBall2.push(rondomNum);
        }
      },
      Rondom2(){
        this.rondomRedBall();
        this.rondomTwoRedBall();
        if((this.redBall[0]+1) === (this.twoRedBall[0]+1)){
          this.Rondom2();
        }else {
          this.redBall2.push(this.num(this.redBall[0]+1));
          this.twoRedBall2.push(this.num(this.twoRedBall[0]+1));
        }
        this.betsNum2 = 1;
      },
      Rondom3(){
        this.rondomRedBall();
        this.rondomTwoRedBall();
        this.rondomThreeRedBall();
        if((this.redBall[0]+1) === (this.twoRedBall[0]+1)||(this.redBall[0]+1) === (this.threeRedBall[0]+1)||(this.twoRedBall[0]+1) === (this.threeRedBall[0]+1)){
          this.Rondom3();
        }else {
          this.redBall2.push(this.num(this.redBall[0]+1));
          this.twoRedBall2.push(this.num(this.twoRedBall[0]+1));
          this.threeRedBall2.push(this.num(this.threeRedBall[0]+1));
        }
        this.betsNum2 = 1;
      },
      //添加选择进入票池
      addLottery(){
        let params = {
          playName:'',
          playType:'',
          code:'',
          multiple:'',
          money:'',
          investCount:'',
          methodNumber:'',
        };
        if(this.betsNum !== 0||this.qrzx !== 0){
          if(this.twoRedBall2.length!==0){
            params.code = this.redBall2+'|'+this.twoRedBall2;
            if(this.threeRedBall2.length!==0){
              params.code = this.redBall2+'|'+this.twoRedBall2+'|'+this.threeRedBall2;
            }
          }else {
            params.code = this.redBall2;
          }
          if(this.twoRedBall2.length!==0){
            params.money = this.betsNum2*2*this.times;
            if(this.threeRedBall2.length!==0){
              params.money = this.betsNum2*2*this.times;
            }
          }else {
            params.money = this.betsNum*2*this.times;
          }

          //判断投注方式
          if(this.tableDemo.method==='任一'){
            if(this.redBall2.length > 1){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任二'||this.tableDemo.method==='前二组选'){
            if(this.redBall2.length > 2){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任三'||this.tableDemo.method==='前三组选'){
            if(this.redBall2.length > 3){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任四'){
            if(this.redBall2.length > 4){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任五'){
            if(this.redBall2.length > 5){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任六'){
            if(this.redBall2.length > 6){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任七'){
            if(this.redBall2.length > 7){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='任八'){
            if(this.redBall2.length > 8){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='前二直选'){
            if(this.redBall2.length > 1||this.twoRedBall2.length > 1){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }else if(this.tableDemo.method==='前三直选'){
            if(this.redBall2.length > 1||this.twoRedBall2.length > 1||this.threeRedBall2.length > 1){
              params.playType = 2
            }else {
              params.playType = 1
            }
          }
          if(this.tableDemo.method==='前二直选'||this.tableDemo.method==='前三直选'){
            params.investCount = this.qrzx;
          }else {
            params.investCount = this.betsNum;
          }
          params.playName = this.tableDemo.method;
          params.multiple = this.tableDemo.multiple;
          params.methodNumber = this.tableDemo.methodNumber;
          console.log(this.times);
          this.tableData.unshift(params);
          this.redBall = [];
          this.redBall2 = [];
          this.twoRedBall = [];
          this.twoRedBall2 = [];
          this.threeRedBall = [];
          this.threeRedBall2 = [];
          this.betsNum = 0;
          this.betsNum2 = 0;
          this.times = 1;
          this.tableDemo.multiple = this.times;
          this.totalPrice += params.money;
          console.log(this.tableData);
        }else {
          console.log("不能添加")
        }
      },
      // 阶乘
      jC(num){
        var sum = 1;
        for(var i=1;i<=num;i++){
          sum = sum*i
        }
        return sum
      },
      // 提交最终投注
      subAll(){
        let _this = this;
        if(localStorage.getItem('userId')){
          console.log(_this.tableData);
          let userId = localStorage.getItem('userId');
          let params = {
            playName:'',
            playType:'',
            code:'',
            multiple:'',
            money:'',
            investCount:'',
            methodNumber:'',
          };
          if(this.betsNum !== 0||this.qrzx !== 0){
            if(this.twoRedBall2.length!==0){
              params.code = this.redBall2+'|'+this.twoRedBall2;
              if(this.threeRedBall2.length!==0){
                params.code = this.redBall2+'|'+this.twoRedBall2+'|'+this.threeRedBall2;
              }
            }else {
              params.code = this.redBall2;
            }
            if(this.twoRedBall2.length!==0){
              params.money = this.betsNum2*2*this.times;
              if(this.threeRedBall2.length!==0){
                params.money = this.betsNum2*2*this.times;
              }
            }else {
              params.money = this.betsNum*2*this.times;
            }

            //判断投注方式
            if(this.tableDemo.method==='任一'){
              if(this.redBall2.length > 1){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任二'||this.tableDemo.method==='前二组选'){
              if(this.redBall2.length > 2){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任三'||this.tableDemo.method==='前三组选'){
              if(this.redBall2.length > 3){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任四'){
              if(this.redBall2.length > 4){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任五'){
              if(this.redBall2.length > 5){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任六'){
              if(this.redBall2.length > 6){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任七'){
              if(this.redBall2.length > 7){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='任八'){
              if(this.redBall2.length > 8){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='前二直选'){
              if(this.redBall2.length > 1||this.twoRedBall2.length > 1){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }else if(this.tableDemo.method==='前三直选'){
              if(this.redBall2.length > 1||this.twoRedBall2.length > 1||this.threeRedBall2.length > 1){
                params.playType = 2
              }else {
                params.playType = 1
              }
            }
            if(this.tableDemo.method==='前二直选'||this.tableDemo.method==='前三直选'){
              params.investCount = this.qrzx;
            }else {
              params.investCount = this.betsNum;
            }
            params.playName = this.tableDemo.method;
            params.multiple = this.tableDemo.multiple;
            params.methodNumber = this.tableDemo.methodNumber;
            console.log(this.times);
            this.tableData.unshift(params);
            this.redBall = [];
            this.redBall2 = [];
            this.twoRedBall = [];
            this.twoRedBall2 = [];
            this.threeRedBall = [];
            this.threeRedBall2 = [];
            this.betsNum = 0;
            this.betsNum2 = 0;
            this.times = 1;
            this.tableDemo.multiple = this.times;
            this.totalPrice += params.money;
            console.log(this.tableData);

            let pid = '';
            let paramsPid = {};
            _this.$http.post('/getElevenselectfiveInfo',paramsPid).then((res)=>{
              pid = res.data.info.pid;
              console.log(pid);
              if(pid !== ''){
                let paramsSubmit = {
                  userId: userId,
                  lotteryName: 'C511',
                  issueNumber: pid,
                  tableData: _this.tableData,
                };
                _this.$http.post('/bettingElevenselectfive',paramsSubmit).then(function (res) {
                  console.log(res.data);
                  _this.tableData = [];
                },function (err) {
                  console.log(err);
                });
              }else {
                console.log('没有pid')
              }
            },(err)=>{
              console.log(err);
            })
          }else {
            _this.$message.error({
              showClose: true,
              message: '请至少选择一注',
            });
          }
        }else {
          this.login = true;
          this.out = false;
          this.model = true;
        }
      },
      // 组合
      zH(all,choseNum){
        return this.jC(all)/(this.jC(choseNum)*this.jC(all-choseNum))
      },
      // 胆拖组合
      dT(ggg,twoRowBall,oneRowBall){
        return (ggg-this.jC(oneRowBall))/this.jC(twoRowBall)
      },
      // 减倍数
      reduce(){
        this.times--;
        if(this.times<1){
          this.times=1;
        }
        this.tableDemo.multiple = this.times;
      },
      // 增加倍数
      add(){
        this.times++;
        if(this.times>99){
          this.times=99;
        }
        this.tableDemo.multiple = this.times;
      },
      // 最大倍数
      maxAdd(){
        this.times = 99;
        this.tableDemo.multiple = this.times;
      },
    },
    updated(){
      if(this.tableDemo.method === '任一'){
        if(this.redBall.length < 1){
          this.betsNum = 0;
        }else {
          this.betsNum = Math.round(this.zH(this.redBall.length,this.methodNum));
        }
      }else if(this.tableDemo.method === '任二'||this.tableDemo.method === '前二组选'){
        if(this.redBall.length < 2){
          this.betsNum = 0;
        }else {
          this.betsNum = Math.round(this.zH(this.redBall.length,this.methodNum));
        }
      }else if(this.tableDemo.method === '前二直选'){
        if(this.redBall.length === 0||this.twoRedBall.length === 0){
          this.qrzx = 0;
        }else {
          this.qrzx = 1;
        }
      }else if(this.tableDemo.method === '前三直选'){
        if(this.redBall.length === 0||this.twoRedBall.length === 0||this.threeRedBall.length === 0){
          this.qrzx = 0;
        }else {
          this.qrzx = 1;
        }
      }else {
        this.betsNum = Math.round(this.zH(this.redBall.length,this.methodNum));
      }
      if(this.times>99){
        this.times=99;
      }

    }
  }
</script>

<style scoped>
  .home{
    position: relative;
  }
  .left{
    width: 85%;
  }
  .header{
    width: 100%;
    height: 60px;
    line-height: 60px;
    background: white;
    text-align: left;
    padding-left: 40px;
    border-bottom: 1px solid #E4E4E4;
  }
  .header span{
    color: red;
    padding: 0 5px;
  }
  .type_select{
    padding: 20px 30px;
    background: white;
    border-bottom: 1px solid #E4E4E4;
  }
  .type_select .select_btn .method_type{
    display: inline-block;
    width: 100px;
    height: 40px;
    line-height: 40px;
    margin: 10px 10px;
    text-align: center;
    border: 1px solid #ccc9c9;
    border-radius: 5px;
  }
  .classname{
    background: red;
    color: white;
  }
  .tips{
    background: white;
    padding: 10px 0 10px 40px;
    border-bottom: 1px solid #e4e4e4;
  }

  .box{
    position: relative;
    height: 37.35vh;
    padding: 10px 40px;
  }
  .box ul{
    padding: 10px 0;
  }
  .box li{
    display: inline-block;
    width:60px;
    height:60px;
    line-height: 60px;
    text-align: center;
    border-radius: 50%;
    margin-right:25px;
  }
  .box .head li{
    border:1px solid #E4E4E4;
    color: red;
  }
  .box .betting_demo{
    position: absolute;
    left: 40px;
    right: 0;
    bottom: 0;
    height: 50px;
    line-height: 50px;
  }

  .right{
    position: absolute;
    top: 0;
    left: 85%;
    bottom: 0;
    width: 15%;
    border-left: 1px solid #E4E4E4;
    background: white;
    text-align: center;
    padding: 50px 0;
  }
  .right .reduce,
  .right .add{
    display: inline-block;
    width: 50px;
    height: 50px;
    line-height: 50px;
    margin-top: 10px;
    border-radius: 5px;
    border: 1px solid #d1d1d5;
    vertical-align: top;
  }
  .max_add{
    display: inline-block;
    width: 60px;
    height: 50px;
    line-height: 50px;
    margin-top: 10px;
    border-radius: 5px;
    border: 1px solid #d1d1d5;
    vertical-align: top;
  }
  .right .reduce:active,
  .right .add:active,
  .right .dancing_drag:active,
  .right .random_lottery:active,
  .right .add_lottery:active,
  .right .history:active,
  .right .my_order:active,
  .right .quit:active,
  .right .save:active,
  .max_add:active{
    background: #e4e4e4;
  }
  .right .times{
    width: 80px;
    height: 50px;
    margin: 10px 10px 0;
    border-radius: 5px;
    border: 1px solid #d1d1d5;
    text-align: center;
    font-size: 20px;
  }
  .right .dancing_drag,
  .right .random_lottery,
  .right .add_lottery,
  .right .history,
  .right .my_order,
  .right .quit,
  .right .save{
    width: 100px;
    height: 50px;
    line-height: 50px;
    margin: 40px auto;
    border-radius: 5px;
    border: 1px solid #d1d1d5;
  }
  .right a .my_order{
    color: black;
  }

  footer{
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    background: white;
    padding: 30px 0 30px 20px;
    border-top: 1px solid #E4E4E4;
  }
  .footer_text{
    display: inline-block;
    height: 50px;
    line-height: 50px;
  }
  .delete_all{
    float: right;
    width: 100px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    margin-right: 5%;
    border: 1px solid #ccc9c9;
    border-radius: 5px;
    background: white;
    outline: none;
    font-size: 20px;
  }
  .delete_all:active{
    background: #e4e4e4;
  }
  .sub_btn{
    float: right;
    width: 100px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    margin-right: 5%;
    background: red;
    border-radius: 5px;
    color: white;
  }

  .dan1, .tuo2{
    margin: 10px 20px;
  }

  /*登录、退出、弹窗*/
  .login,.out{
    display: inline-block;
    width: 100px;
    height: 50px;
    line-height: 50px;
    border-radius: 5px;
    border: 1px solid #ccc9c9;
  }
  .login:active,.out:active{
    background: #e4e4e4;
  }

  .bg_model{
    position: fixed;
    left:0;
    top:0;
    right:0;
    bottom:0;
    background: rgba(0,0,0,0.6);
    z-index: 100;
  }
  .login-wrap{
    /*position: absolute;*/
    width:100%;
    height:100%;
  }
  .ms-login{
    position: absolute;
    left:0;
    top:0;
    right:0;
    bottom:0;
    width:20%;
    height:30%;
    margin: auto;
    padding:40px;
    border-radius: 5px;
    background: #fff;
  }
  .login-btn{
    text-align: center;
  }
  .login-btn button{
    width:100%;
    height:50px;
  }
  .ms-input{
    width: 100%;
    height: 50px;
    line-height: 50px;
    border-radius: 5px;
    border: 1px solid #ccc9c9;
    font-size: 20px;
    padding: 0 10px;
    outline: none;
  }

</style>
