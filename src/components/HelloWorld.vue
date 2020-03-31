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
          <div class="mr-4 p-4 svg--border"
          v-if="$refs.audio"
          :class="{'bg-orging':!$refs.audio.muted}"
          @click="$refs.audio.muted = !$refs.audio.muted">
          <img src="../assets/img/icon-bell.svg" alt="" srcset="">
          </div>
          <!-- 播放或停止-->
          <div class="mr-4 p-4 bg-light svg--border" @click="play">
          <img v-if="isStart" src="../assets/img/icon-pause--orange.svg" alt="" srcset="">
          <img v-else src="../assets/img/icon-play--orange.svg" alt="" srcset="">
          </div>
          <!-- 重置-->
          <div class="mr-4 p-4 svg--border" @click="clear">
          <img src="../assets/img/icon-delete.svg" alt="" srcset="">
          </div>
        </div>
      </div>
      <!-- 任務標語 -->
      <div v-if="isStart" class="h2 text-white font-weight-bold pt-5">GOGO~~~奮鬥下去!!</div>
      <div v-else class="h2 text-white font-weight-bold pt-5">休息是為了走更遠的路</div>
    </div>
    <!-- 固定下方的番茄圖 -->
    <div class="fixbottom d-flex justify-content-center">
      <img v-if="isStart" src="../assets/img/tomato--orange.svg" alt="" srcset="">
      <img v-else src="../assets/img/tomato--green.svg" alt="" srcset="">
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
      const vm = this;
      if (vm.isStart) {
        clearInterval(vm.interval);
        vm.$refs.audio.pause();
      } else {
        vm.interval = setInterval(() => {
          vm.counter();
        }, 1000);
        vm.$refs.audio.play();
      }
      vm.isStart = !vm.isStart;
    },
    clear() {
      const vm = this;
      vm.$refs.audio.load();
      clearInterval(vm.interval);
      vm.setSecond = 1500;
      vm.minutes = '25';
      vm.seconds = '00';
      vm.isStart = false;
    },
    counter() {
      const vm = this;
      const min = Math.floor(Math.floor(vm.setSecond % 3600) / 60);
      const sec = vm.setSecond % 60;
      if (vm.setSecond > 0) {
        vm.setSecond -= 1;

        // 分數格式調整
        if (min < 10) {
          vm.minutes = `0${min}`;
        } else {
          vm.minutes = min;
        }

        // 秒數格式調整
        if (sec < 10) {
          vm.seconds = `0${sec}`;
        } else {
          vm.seconds = sec;
        }
      } else {
        vm.clear();
      }
    },
  },
  computed: {
    fullDate() {
      const vm = this;
      const year = vm.date.getFullYear();
      const month = vm.date.getMonth() + 1;
      const day = vm.date.getDate();
      const hour = vm.date.getHours();
      const min = vm.date.getMinutes();
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
