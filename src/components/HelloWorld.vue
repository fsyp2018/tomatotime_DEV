<template>
  <div>
    <div>
      <!-- 顯示目前日期與時間 -->
    <div class="d-flex flex-row-reverse bd-highlight pr-5 pt-3">
    <p class="text-white font-weight-bold">{{fullDate}}</p>
    </div>
    <!-- 倒數計時器與任務標語 -->
    <div class="row justify-content-around">
      <!-- 倒數計時器 -->
      <div>
        <div class="clock font-weight-bold">
          {{minutes}}:{{seconds}}
          </div>
          <!-- 控制器 -->
        <div class="d-flex justify-content-center">
          <!-- 聲音 -->
          <div class="mr-4 p-4 bg-orging svg--border">
          <img src="../assets/img/icon-bell.svg" alt="" srcset="">
          </div>
          <!-- 播放或停止-->
          <div class="mr-4 p-4 bg-light svg--border" @click="play">
          <img src="../assets/img/icon-play--orange.svg" alt="" srcset="">
          </div>
          <!-- 重置-->
          <div class="mr-4 p-4 svg--border">
          <img src="../assets/img/icon-delete.svg" alt="" srcset="">
          </div>
        </div>
      </div>
      <!-- 任務標語 -->
      <div class="h2 text-white font-weight-bold pt-5">休息是為了走更遠的路</div>
    </div>
    <!-- 固定下方的番茄圖 -->
    <div class="fixbottom d-flex justify-content-center">
      <img src="../assets/img/tomato--green.svg" alt="" srcset="">
    </div>
    <audio ref="audio" src="../assets/music/01.mp3" type="audio/mpeg" loop="loop"></audio>
    </div>
  </div>
</template>

<script>
import $ from 'jquery';

export default {
  name: 'HelloWorld',
  data() {
    return {
      isStart: false,
      setSecond: 1500, // 初始共幾秒,
      minutes: '25', // 字幕顯示(分)
      seconds: '00', // 字幕顯示(秒)
      date: new Date(),
      interval: '',
    };
  },
  methods: {
    openModal() {
      $('#exampleModal').modal('show');
    },
    play() {
      this.$refs.audio.play();
      console.log('play');
    },
  },
  computed: {
    fullDate() {
      const year = this.date.getFullYear();
      const month = this.date.getMonth() + 1;
      const day = this.date.getDate();
      const hour = this.date.getHours();
      const min = this.date.getMinutes();
      const str = `${year}.${month}.${day} ${hour < 10 ? '0' : ''}
      ${hour}:${min < 10 ? '0' : ''}${min}`;
      return str;
    },
  },
  created() {
    const vm = this;
    setInterval(() => {
      vm.date = new Date();
    }, 1000);
  },
};
</script>
