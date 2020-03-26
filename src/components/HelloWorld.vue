<template>
  <div class="bgcolor">
    <div>
    <div class="d-flex flex-row-reverse bd-highlight mr-5 pt-3">
    <p class="text-white">{{fullDate}}</p>
    </div>
    <div class="header row justify-content-around">
      <div>
        <div class="clock border border-primary">{{minutes}}:{{seconds}}</div>
      </div>
      <div class="h1 text-white border border-primary">休息是為了走更遠的路</div>
    </div>
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
