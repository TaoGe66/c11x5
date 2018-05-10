<template>
  <section class="home" :class="typeTab=='GAME'?'gameType':(typeTab=='TD'?'lotteryType':'remoteType')">
    <header class="header" :class="typeTab=='GAME'?'gameType':(typeTab=='TD'?'lotteryType':'remoteType')">
      距离<span v-text="timeInfo.pid"></span>期截止时间还剩<span>{{content}}</span><img class="head_img" src="./title_jindou.png" alt="" v-show="typeTab=='GAME'"><span style="float: right;color: white;" v-show="kjNum">第{{oldInfo.pid}}期开奖结果 <div class="old_num" v-for="itemOld in oldInfo.awardcode">{{itemOld}}</div></span>
    </header>
    <!--左侧操作-->
    <div class="left">
      <div v-title>11选5主页面</div>
      <div class="type_select">
        <ul class="select_btn">
          <li v-for="(item,index) in methodType" @click="type(index,item)" class="method_type" :class="{classname: index==qwerqwre}">{{item.name}}</li>
        </ul>
      </div>
      <!--<div class="tips" v-model="tipsContent">{{tipsContent}}</div>-->
      <div class="tips" v-model="tipsContent" v-if="typeTab=='TD'">{{tipsContent}}</div>
      <div class="tips2" v-model="tipsContent" v-if="typeTab=='GAME'">{{tipsContent}}</div>

      <!--选号区-->
      <div class="box" v-show="normal">
        <ul class="head">
          <li v-for="(item,index) in firstRow" @click="chooseRed(index,item)" :style="{background:redBall.indexOf(index)==-1?'#ecd6fa':'#ff51aa',color:redBall.indexOf(index)==-1?'red':'white'}" >{{item}}</li>
        </ul>
        <ul class="head" v-show="twoZhi">
          <li v-for="(item,index) in twoRow" @click="twoChooseRed(index,item)" :style="{background:twoRedBall.indexOf(index)==-1?'#ecd6fa':'#ff51aa',color:twoRedBall.indexOf(index)==-1?'red':'white'}" >{{item}}</li>
        </ul>
        <ul class="head" v-show="threeZhi">
          <li v-for="(item,index) in threeRow" @click="threeChooseRed(index,item)" :style="{background:threeRedBall.indexOf(index)==-1?'#ecd6fa':'#ff51aa',color:threeRedBall.indexOf(index)==-1?'red':'white'}" >{{item}}</li>
        </ul>
        <p v-show="multipleNumber" class="betting_demo" v-if="typeTab=='GAME'">您选了<span> {{betsNum}} </span>注，共<span> {{betsNum*2*times*100}} </span>金豆</p>
        <p v-show="multipleNumber2" class="betting_demo" v-if="typeTab=='GAME'">您选了<span> {{betsNum2}} </span>注，共<span> {{betsNum2*2*times*100}} </span>金豆</p>
        <p v-show="multipleNumber" class="betting_demo" v-if="typeTab=='TD'">您选了<span> {{betsNum}} </span>注，共<span> {{betsNum*2*times*100}} </span>积分</p>
        <p v-show="multipleNumber2" class="betting_demo" v-if="typeTab=='TD'">您选了<span> {{betsNum2}} </span>注，共<span> {{betsNum2*2*times*100}} </span>积分</p>
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
        <p class="betting_demo">您选了<span> {{betsNum}} </span>注，共<span> {{betsNum*2*times*100}} </span>积分</p>
      </div>

      <!--票池-->
      <div class="show_select">
        <el-table
          :data="tableData"
          :class="typeTab=='GAME'?'gameType':(typeTab=='TD'?'lotteryType':'remoteType')"
          style="width: 100%"
          height="330">
          <el-table-column type="index" width="100" label="序号" header-align="center" align="center">
          </el-table-column>
          <el-table-column prop="methodNumber" label="玩法" width="150" header-align="center" align="center">
          </el-table-column>
          <el-table-column prop="playType" label="单/复式" width="150" header-align="center" align="center">
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
          <el-table-column prop="money" :label="typeTab=='GAME'?'金豆':'积分'" width="150" header-align="center" align="center">
            <template slot-scope="scope">
              {{scope.row.money*100}}
            </template>
          </el-table-column>
          <el-table-column label="操作" width="250" header-align="center" align="center">
            <template slot-scope="scope">
              <el-button @click="deleteOne(scope.row,scope.$index)" type="text" size="medium">删除</el-button>
              <!--<el-button type="text" size="medium">编辑</el-button>-->
            </template>
          </el-table-column>
        </el-table>
      </div>
    </div>

    <!--右侧操作-->
    <!--<div class="right" v-show="normal">
      &lt;!&ndash;<span class="login" v-show="login" @click="loginShow">登录</span><span class="out" v-show="out" @click="loginOut">退出</span>
      <p class="history">历史开奖</p>
      <router-link to="/order"><p class="my_order">我的订单</p></router-link>&ndash;&gt;

      <p class="multiple_input">倍数</p>
      <div>
        投 <span class="reduce" @click="reduce()">－</span><input type="number" class="times" v-model="times"><span class="add" @click="add()" >＋</span> 倍
      </div>
      <p class="max_add" @click="maxAdd()">99倍</p>
      &lt;!&ndash;<p class="dancing_drag" @click="showDanTuo">胆拖</p>&ndash;&gt;
      <p class="random_lottery" @click="Rondom1" v-show="rondom1" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">机选</p>
      <p class="random_lottery" @click="Rondom2" v-show="rondom2" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">机选</p>
      <p class="random_lottery" @click="Rondom3" v-show="rondom3" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">机选</p>
      <p class="add_lottery" @click="addLottery">添加</p>
    </div>-->


    <div class="right" :class="typeTab=='GAME'?'gameType':(typeTab=='TD'?'lotteryType':'remoteType')" v-show="normal">
      <img style="width: 200px;margin-bottom: 100px" src="./jindou.png" alt="" v-show="typeTab=='GAME'">
      <p class="random_lottery" @click="Rondom1" v-show="rondom1" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">机选一注</p>
      <p class="random_lottery" @click="Rondom2" v-show="rondom2" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">机选一注</p>
      <p class="random_lottery" @click="Rondom3" v-show="rondom3" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">机选一注</p>
      <div>
        投 <span class="reduce" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')" @click="reduce()">－</span><input type="number" class="times" v-model="times" :class="typeTab=='GAME'?'gameType':(typeTab=='TD'?'lotteryType':'remoteType')"><span class="add" @click="add()" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">＋</span> 倍
      </div>
      <p class="max_add" @click="maxAdd()" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">99倍</p>
      <!--<p class="dancing_drag" @click="showDanTuo">胆拖</p>-->
      <p class="add_lottery" @click="addLottery" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">确认选号</p>
    </div>

    <div class="right" v-show="dancingDrag">
      <!--<span class="login" v-show="login" @click="loginShow">登录</span><span class="out" v-show="out" @click="loginOut">退出</span>
      <p class="history">历史开奖</p>
      <p class="my_order">我的订单</p>-->
      <p class="multiple_input">倍数</p>
      <div>
        投 <span class="reduce" @click="reduce()">－</span><input type="text" class="times" v-model="times"><span class="add" @click="add()" >＋</span> 倍
      </div>
      <p class="save" @click="saveDanTuo">保存</p>
      <p class="quit" @click="quit">退出</p>
    </div>

    <!--底部-->
    <!--<footer>
      <p class="footer_text">共计金额：<span>{{totalPrice*100}} 积分</span></p><span class="sub_btn" @click="subAll" v-show="submitBtn1" :class="{isSub_btn,isSub_btn_red}">投注</span><span class="sub_btn2" v-show="submitBtn2">投注</span><button @click="deleteAll" class="delete_all">清空票池</button>
    </footer>-->
    <footer :class="typeTab=='GAME'?'gameType':(typeTab=='TD'?'lotteryType':'remoteType')">
      <button @click="deleteAll" class="delete_all" :class="typeTab=='GAME'?'gameType_btn':(typeTab=='TD'?'lotteryType_btn':'remoteType_btn')">清空选号</button><span class="sub_btn" @click="subAll" v-show="submitBtn1" :class="{isSub_btn,isSub_btn_red}">提交订单</span><span class="sub_btn2" v-show="submitBtn2">提交订单</span><p class="footer_text">共计金额：<span v-show="typeTab=='GAME'">{{totalPrice*100}} 金豆</span><span v-show="typeTab=='TD'">{{totalPrice*100}} 积分</span></p>
    </footer>

    <!--投注截止时间到弹窗-->
    <div class="login-wrap" v-show="model">
      <div class="bg_model">
        <div class="ms-login">
          <p class="model_tips">温馨提示</p>
          <p class="model_content">本期时间已截止</p>
          <p class="model_btn" @click="modelHide()">确定</p>
        </div>
      </div>
    </div>
    <!--切换模式弹窗-->
    <div class="login-wrap" v-show="typeTabModel">
      <div class="bg_model">
        <div class="ms-login">
          <p class="model_tips">温馨提示</p>
          <p class="model_content">当前模式已经切换</p>
          <p class="model_btn" @click="typeTabModelHide()">确定</p>
        </div>
      </div>
    </div>
    <!--加载中tips-->
    <div class="load_tips" v-show="loadTips">正在加载中...</div>
  </section>
</template>

<script>
  export default{
    data(){
      return{
        typeTab:'GAME',
        login: true,
        out: false,
        model: false,
        typeTabModel: false,
        loadTips:false,
        kjNum: true,
        newPid:'',
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
          st:'',
        },
        awardList:[],
        oldInfo:{
          pid:'',
          awardcode:'',
        },
        methodType:[{
          name:'任五',
          tips:'从01～11中任选5个或多个号码，所选号码与开奖号码全部相同，即中奖540元。',
          tips2:'从01～11中任选5个或多个号码，所选号码与开奖号码全部相同，即中奖54000金豆。'
        },{
          name:'任二',
          tips:'从01～11中任选2个或多个号码，所选号码与开奖号码任意两个号码相同，即中奖6元。',
          tips2:'从01～11中任选2个或多个号码，所选号码与开奖号码任意两个号码相同，即中奖600金豆。',
        },{
          name:'任三',
          tips:'从01～11中任选3个或多个号码，所选号码与开奖号码任意三个号码相同，即中奖19元。',
          tips2:'从01～11中任选3个或多个号码，所选号码与开奖号码任意三个号码相同，即中奖1900金豆。',
        },{
          name:'任四',
          tips:'从01～11中任选4个或多个号码，所选号码与开奖号码任意四个号码相同，即中奖78元。',
          tips2:'从01～11中任选4个或多个号码，所选号码与开奖号码任意四个号码相同，即中奖7800金豆。',
        },{
          name:'任六',
          tips:'从01～11中任选6个或多个号码，所选号码与开奖号码五个号码相同，即中奖90元。',
          tips2:'从01～11中任选6个或多个号码，所选号码与开奖号码五个号码相同，即中奖9000金豆。',
        },{
          name:'任七',
          tips:'从01～11中任选7个或多个号码，所选号码与开奖号码五个号码相同，即中奖26元。',
          tips2:'从01～11中任选7个或多个号码，所选号码与开奖号码五个号码相同，即中奖2600金豆。',
        },{
          name:'任八',
          tips:'从01～11中任选8个或多个号码，所选号码与开奖号码五个号码相同，即中奖9元。',
          tips2:'从01～11中任选8个或多个号码，所选号码与开奖号码五个号码相同，即中奖900金豆。',
        },{
          name:'前一',
          tips:'从01～11中任选1个或多个号码，所选号码与开奖号码第一位号码相同，即中奖13元。',
          tips2:'从01～11中任选1个或多个号码，所选号码与开奖号码第一位号码相同，即中奖1300金豆。',
        },{
          name:'前二直选',
          tips:'猜中开奖号码前两位（且顺序一致），即中奖130元。',
          tips2:'猜中开奖号码前两位（且顺序一致），即中奖13000金豆。',
        },{
          name:'前三直选',
          tips:'猜中开奖号码前三位（且顺序一致），即中1170元。',
          tips2:'猜中开奖号码前三位（且顺序一致），即中117000金豆。',
        },{
          name:'前二组选',
          tips:'从01～11中任选2个或多个号码，所选号码与开奖号码前两位号码相同（顺序不限），即中奖65元。',
          tips2:'从01～11中任选2个或多个号码，所选号码与开奖号码前两位号码相同（顺序不限），即中奖6500金豆。',
        },{
          name:'前三组选',
          tips:'从01～11中选3或多个号码，选号与开奖号码前三位相同（顺序不限），即中奖195元。',
          tips2:'从01～11中选3或多个号码，选号与开奖号码前三位相同（顺序不限），即中奖19500金豆。',
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
        uid:'',
        deviceNo:'',
        isSub_btn:true,
        isSub_btn_red:false,
        submitBtn1:true,
        submitBtn2:false,
        httpApi:'http://v3api.hcb66.com',
      }
    },
    created(){
      let _this = this;
//      this.tipsContent = this.methodType[0].tips;
      this.tableDemo.method = this.methodType[0].name;
      this.tableDemo.multiple = this.times;
//      localStorage.removeItem('userId');
      // 获取query参数
      this.GetRequest();
      this.query();
      setInterval(function () {
        _this.getInfo();
      },30000);
      setInterval(function () {
        _this.getType();
      },2000)
    },
    methods:{
      // 获取query参数
      GetRequest(){
        this.uid = this.$route.query.uid;
        this.deviceNo = this.$route.query.deviceNo;
//        alert(this.uid);
//        alert(this.deviceNo);
      },

      //查询期号和开奖时间
      query(){
        let _this = this;
        let params = {
//          deviceNo:this.deviceNo,
          deviceNo:"0123456789ABCDE2",
        };
        _this.startInfo = [];
        _this.timeInfo.et = '';
        _this.timeInfo.st = '';
        _this.$http.post('/happyEleven/queryCurrIssue',params).then((res)=>{
          _this.startInfo = res.data;
//          console.log(_this.startInfo);
          _this.timeInfo.pid = _this.startInfo.info.pid;
          _this.timeInfo.et = _this.startInfo.info.at;
          _this.timeInfo.st = _this.startInfo.info.st;
          _this.typeTab = _this.startInfo.info.type;
          if(_this.typeTab == 'TD'){
            _this.tipsContent = _this.methodType[0].tips;
          }
          if(_this.typeTab == 'GAME'){
            _this.tipsContent = _this.methodType[0].tips2;
          }
          localStorage.setItem("localTypeTab",_this.typeTab);
          _this.getInfo();
          _this.addTime();
        },(err)=>{
          console.log(err);
        });
      },
      //获取历史开奖信息
      getInfo(){
        let _this = this;
        let cparams = {
          issueNumber:_this.timeInfo.pid,
          lottertId:"C511",
        };
        _this.$http.post('/happyEleven/queryAwardNumberList',cparams).then((res)=>{
//          console.log(res.data);
          _this.oldInfo = res.data.awardInfo;
          if(_this.oldInfo.awardcode === ''){
            _this.oldInfo.awardcode = ['--','--','--','--','--'];
          }else {
            _this.oldInfo.awardcode = _this.oldInfo.awardcode.split(',');
          }
        },(err)=>{
          console.log(err);
        });
      },
      //获取模式
      getType(){
        let _this = this;
        let params = {
//          deviceNo:this.deviceNo,
          deviceNo:"0123456789ABCDE2",
        }
//        _this.$http.post('http://v3api.hcb66.com/device/mode/query',params).then(function (res) {
        _this.$http.post('http://dev.hcb66.com:7300/device/mode/query',params).then(function (res) {
//          console.log(res.data);
          if(res.data.status == 10000){
//            console.log(_this.tableDemo.method);
            _this.typeTab = res.data.data;
            let localTypeTab = localStorage.getItem("localTypeTab");
            if(localTypeTab == _this.typeTab){
              _this.typeTabModel = false;
            }else {
              _this.typeTabModel = true;
              if(_this.typeTab == 'TD'){
                if(_this.tableDemo.method == '任五'){
                  _this.tipsContent = _this.methodType[0].tips;
                }
                if(_this.tableDemo.method == '任二'){
                  _this.tipsContent = _this.methodType[1].tips;
                }
                if(_this.tableDemo.method == '任三'){
                  _this.tipsContent = _this.methodType[2].tips;
                }
                if(_this.tableDemo.method == '任四'){
                  _this.tipsContent = _this.methodType[3].tips;
                }
                if(_this.tableDemo.method == '任六'){
                  _this.tipsContent = _this.methodType[4].tips;
                }
                if(_this.tableDemo.method == '任七'){
                  _this.tipsContent = _this.methodType[5].tips;
                }
                if(_this.tableDemo.method == '任八'){
                  _this.tipsContent = _this.methodType[6].tips;
                }
                if(_this.tableDemo.method == '前一'){
                  _this.tipsContent = _this.methodType[7].tips;
                }
                if(_this.tableDemo.method == '前二直选'){
                  _this.tipsContent = _this.methodType[8].tips;
                }
                if(_this.tableDemo.method == '前三直选'){
                  _this.tipsContent = _this.methodType[9].tips;
                }
                if(_this.tableDemo.method == '前二组选'){
                  _this.tipsContent = _this.methodType[10].tips;
                }
                if(_this.tableDemo.method == '前三组选'){
                  _this.tipsContent = _this.methodType[11].tips;
                }
              }
              if(_this.typeTab == 'GAME'){
                if(_this.tableDemo.method == '任五'){
                  _this.tipsContent = _this.methodType[0].tips2;
                }
                if(_this.tableDemo.method == '任二'){
                  _this.tipsContent = _this.methodType[1].tips2;
                }
                if(_this.tableDemo.method == '任三'){
                  _this.tipsContent = _this.methodType[2].tips2;
                }
                if(_this.tableDemo.method == '任四'){
                  _this.tipsContent = _this.methodType[3].tips2;
                }
                if(_this.tableDemo.method == '任六'){
                  _this.tipsContent = _this.methodType[4].tips2;
                }
                if(_this.tableDemo.method == '任七'){
                  _this.tipsContent = _this.methodType[5].tips2;
                }
                if(_this.tableDemo.method == '任八'){
                  _this.tipsContent = _this.methodType[6].tips2;
                }
                if(_this.tableDemo.method == '前一'){
                  _this.tipsContent = _this.methodType[7].tips2;
                }
                if(_this.tableDemo.method == '前二直选'){
                  _this.tipsContent = _this.methodType[8].tips2;
                }
                if(_this.tableDemo.method == '前三直选'){
                  _this.tipsContent = _this.methodType[9].tips2;
                }
                if(_this.tableDemo.method == '前二组选'){
                  _this.tipsContent = _this.methodType[10].tips2;
                }
                if(_this.tableDemo.method == '前三组选'){
                  _this.tipsContent = _this.methodType[11].tips2;
                }
              }
              localStorage.setItem("localTypeTab",_this.typeTab);
              setTimeout(function () {
                _this.typeTabModel = false;
//                _this.model = false;
              },5000)
            }
          }
        },function (err) {
          console.log(err);
        })
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
//          console.log(endTime);
//          console.log(nowTime);
          let t = endTime.getTime() - nowTime.getTime();
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
            _this.model = true;
            _this.kjNum = false;
            _this.query();
            setTimeout(function () {
              _this.model = false;
              _this.kjNum = true;

              let localTypeTab = localStorage.getItem("localTypeTab");
              if(localTypeTab == _this.typeTab){
                _this.typeTabModel = false;
//                _this.model = false;
              }else {
                _this.typeTabModel = true;
//                _this.model = false;
              }

            },5000);
            setTimeout(function () {
              _this.typeTabModel = false;
//              _this.model = false;
            },10000);
          }
        },1000);
      },
      /*modelPayShow(){
        this.model2 = true;
      },*/
      //隐藏截止时间到弹窗
      modelHide(){
        this.model = false;
        this.kjNum = true;
        let localTypeTab = localStorage.getItem("localTypeTab");
        if(localTypeTab == this.typeTab){
          this.typeTabModel = false;
          this.model = false;
        }else {
          this.typeTabModel = true;
          this.model = false;
        }
        this.query();
      },
      //隐藏模式切换弹窗
      typeTabModelHide(){
        this.typeTabModel = false;
        this.model = false;
//        this.query();
      },
      //玩法类型选择
      type(index,item){
        this.qwerqwre = index;
        if(this.typeTab == 'TD'){
          this.tipsContent = item.tips;
        }
        if(this.typeTab == 'GAME'){
          this.tipsContent = item.tips2;
        }
//        this.tipsContent = item.tips;
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
        if(item.name === '前一'){
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
        this.tableData = [];
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
          if(this.tableDemo.method==='前一'){
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
            params.investCount = this.betsNum2;
          }else {
            params.investCount = this.betsNum;
          }
          params.playName = this.tableDemo.methodNumber;
          params.multiple = this.times;
          params.methodNumber = this.tableDemo.method;
//          console.log(this.times);
          if(this.times == 0||this.times == ''){
            this.$message.error({
              showClose: true,
              message: '倍数不能为空',
            });
          }else if(params.money>20000){
            this.$message.error({
              showClose: true,
              message: '单注金额不能超过2万',
            });
          }else {
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
            this.totalPrice += params.money;
          }
        }else {
          this.$message.error({
            showClose: true,
            message: '请至少选择一注',
          });
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
        console.log(_this.tableData);
        let params = {
          playName:'',
          playType:'',
          code:'',
          multiple:'',
          money:'',
          investCount:'',
          methodNumber:'',
        };
        if(this.betsNum !== 0||this.betsNum2 !== 0){
          if(this.times == 0||this.times == ''){
            this.times = 1;
          }
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
          if(this.tableDemo.method==='前一'){
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
            params.investCount = this.betsNum2;
          }else {
            params.investCount = this.betsNum;
          }
          params.playName = this.tableDemo.methodNumber;
          params.multiple = this.times;
          params.methodNumber = this.tableDemo.method;
          if(this.times !== ''&&this.times !== 0&&params.money < 20000){
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
          }
        }
        this.submitBtn1 = false;
        this.submitBtn2 = true;
        if(this.betsNum !== 0||this.betsNum2 !== 0||this.totalPrice !== 0){
          let total = this.betsNum*this.times*2;
          let total2 = this.betsNum2*this.times*2;
          if(total > 20000||total2 > 20000){
            _this.submitBtn1 = true;
            _this.submitBtn2 = false;
            _this.$message.error({
              showClose: true,
              message: '单注金额不能超过200万',
            });
          }else {
            let paramsPid = {
              deviceNo:this.deviceNo,
//              deviceNo:"0123456789ABCDE2",
            };
            this.loadTips = true;
            _this.$http.post('/happyEleven/queryCurrIssue',paramsPid).then((res)=>{
              console.log(res);
              if(res.data.code == 0){
                _this.newPid = res.data.info.pid;
//              console.log(_this.newPid);
                _this.tzSub();
              }else {
                console.log('没有pid')
              }
            },(err)=>{
              console.log(err);
            })
          }
        }
        if(this.betsNum == 0&&this.betsNum2 == 0&&this.tableData.length == 0){
          _this.submitBtn1 = true;
          _this.submitBtn2 = false;
          _this.$message.error({
            showClose: true,
            message: '请至少选择一注',
          });
        }
      },
      // 投注
      tzSub(){
        let _this = this;
        let paramsSubmit = {
          userId: _this.uid,
          deviceNo:_this.deviceNo,
//          userId: '5a72e459f57b05216546d540',
//          deviceNo: '0123456789ABCDE2',
          lotteryName: 'C511',
          issueNumber: _this.newPid,
          moneySum:_this.totalPrice,
          tableData: _this.tableData,
          type:_this.typeTab,
        };
//        console.log(paramsSubmit);
        _this.$http.post('/happyEleven/betting',paramsSubmit).then(function (res) {
          console.log(res.data);
          let msg = res.data.data;
          if(res.data.code == 10000){
            _this.loadTips = false;
            _this.toastMessage(msg);
            _this.callBackPay();
            _this.submitBtn1 = true;
            _this.submitBtn2 = false;
          }
        },function (err) {
          console.log(err);
        });
      },
      callBackPay(){
        this.tableData = [];
        this.betsNum = 0;
        this.betsNum2 = 0;
        this.totalPrice = 0;
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
      if(this.tableDemo.method === '前一'){
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
      if(this.betsNum>0||this.qrzx>0||this.totalPrice>0){
        this.isSub_btn = false;
        this.isSub_btn_red = true;
      }else{
        this.isSub_btn = true;
        this.isSub_btn_red = false;
      }
    }
  }
</script>

<style scoped>
  .home{
    position: relative;
    color: white;
  }
  .gameType{
    background: #7034b0;
  }
  .lotteryType{
    background: #2a3040;
  }
  .gameType_btn{
    background: #7a41df;
  }
  .lotteryType_btn{
    background: #373c52;
  }
  .left{
    width: 85%;
  }
  .header{
    position: relative;
    width: 100%;
    height: 60px;
    line-height: 60px;
    text-align: left;
    padding: 0 40px;
    border-bottom: 1px solid #E4E4E4;
  }
  .head_img{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    height: 50px;
    /*vertical-align: top;*/
    /*margin: 25px 0 0 300px;*/
  }
  .header span{
    color: #ff51aa;
    padding: 0 5px;
  }
  .old_num{
    display: inline-block;
    width: 40px;
    height: 40px;
    line-height: 40px;
    border-radius: 50%;
    background: #ff51aa;
    text-align: center;
    margin-left: 10px;
  }
  .type_select{
    padding: 20px 30px;
    /*border-bottom: 1px solid #E4E4E4;*/
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
    background: #ff51aa;
    color: white;
  }
  .tips{
    padding: 0 0 20px 40px;
    border-bottom: 1px solid #e4e4e4;
  }
  .tips2{
    padding: 0 0 20px 40px;
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
    right: 40px;
    bottom: 0;
    height: 50px;
    line-height: 50px;
    color: white;
  }

  .right{
    position: absolute;
    top: 60px;
    left: 85%;
    bottom: 0;
    width: 15%;
    border-left: 1px solid #E4E4E4;
    text-align: center;
    padding: 50px 0 50px;
    color:white;
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
    width: 200px;
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
    background: #46277e;
  }
  .right .times{
    width: 80px;
    height: 50px;
    margin: 10px 10px 0;
    border-radius: 5px;
    border: 1px solid #d1d1d5;
    text-align: center;
    font-size: 20px;
    color: white;
  }
  .right .dancing_drag,
  .right .random_lottery,
  .right .add_lottery,
  .right .history,
  .right .my_order,
  .right .quit,
  .right .save{
    width: 200px;
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
    float: right;
    height: 50px;
    line-height: 50px;
    margin-right: 5%;
  }
  .delete_all{
    width: 100px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    border: 1px solid #ccc9c9;
    border-radius: 5px;
    color: white;
    outline: none;
    font-size: 20px;
  }
  .delete_all:active{
    background: #46277e;
  }
  .sub_btn{
    float: right;
    width: 200px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    margin-right: 2.2%;
    /*background: red;*/
    border-radius: 5px;
    color: white;
  }
  .sub_btn2{
    float: right;
    width: 200px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    margin-right: 2.2%;
    background: #c4c4c4;
    border-radius: 5px;
    color: white;
  }
  .isSub_btn{
    background: #c4c4c4;
  }
  .isSub_btn_red{
    background: #ff51aa;
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
    height:180px;
    margin: auto;
    /*padding:40px;*/
    border-radius: 10px;
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
  .model_tips{
    height: 50px;
    line-height: 50px;
    text-align: center;
    border-bottom: 1px solid #ccc9c9;
    color: black;
  }
  .model_content{
    height: 80px;
    line-height: 80px;
    text-align: center;
    border-bottom: 1px solid #ccc9c9;
    color: black;
  }
  .model_btn{
    width: 100%;
    height: 50px;
    line-height: 50px;
    text-align: center;
    color: red;
  }

  .load_tips{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    width: 150px;
    height: 60px;
    line-height: 60px;
    text-align: center;
    background: white;
    border-radius: 5px;
    color: black;
  }

</style>
