<template>
  <div>
    <div>
      <!-- menu顯示按鈕 -->
    <nav class="navbar navbar-light">
      <img class="svg--border" src="../assets/img/icon-edit.svg" alt="" srcset="" @click="menushow">
    </nav>
    <!-- 警告視窗 -->
    <div class="modal fade bd-example-modal-lg" tabindex="-1"
    role="dialog" aria-labelledby="mySmallModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg">
          <div class="alert bg-orange text-center" role="alert">
              {{ caveat }}
          </div>
      </div>
    </div>
    <!-- menu -->
    <div class="side-menu" :class="{menushow:sidemenushow}">
      <ul class="nav">
        <li>
        <span>新增待辦事項</span>
        <div class="input-group mb-3">
          <input type="text" class="form-control" placeholder="請輸入待辦事項"
          v-model="newTodo" @keyup.enter="addTodo">
          <div class="input-group-prepend">
            <button class="btn btngreen" type="button" @click="addTodo">
              新增
              </button>
          </div>
        </div>
        </li>
        <li>
        <span>設定倒數時間</span>
        <div class="input-group mb-3">
          <p>分鐘</p>
          <input type="number" class="form-control"  min="0" max="60"
          v-model="setminutes">
          <p>秒數</p>
          <input type="number" class="form-control"  min="0" max="60"
          v-model="setseconds">
          <div class="input-group-prepend">
            <button class="btn btngreen" type="button" @click="setTimeCount">
              設定
              </button>
          </div>
        </div>
        </li>
      </ul>
      <div class="container my-3">
        <div class="card text-center">
          <div class="card-header">
            <ul class="nav nav-tabs card-header-tabs">
              <li class="nav-item">
                <a class="nav-link"
                :class="{'active': visibility == 'all'}"
                @click="visibility = 'all'" href="#">待辦事項</a>
              </li>
              <li class="nav-item">
                <a class="nav-link"
                :class="{'active': visibility == 'completed'}"
                @click="visibility = 'completed'" href="#">已完成</a>
              </li>
            </ul>
          </div>
          <ul class="list-group list-group-flush text-left">
            <li class="list-group-item" v-for="(item) in filteredTodos" :key="item.id">
              <div class="d-flex">
                <div class="form-check">
                <div class="radio">
                  <label v-if="visibility === 'all'"><input type="radio" @click="clickradio(item)"
                  v-model="task"
                   :value="item.title" :disabled="timerun"> {{ item.title }}</label>
                   <label v-else>{{ item.title }}</label>
                </div>
                </div>
                <button type="button" class="close ml-auto" aria-label="Close"
                @click="removeTodo(item)">
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
            </li>
          </ul>
          <div class="card-footer">
            <a href="#" @click="deleteList" v-if="checkcount">清除所有任務</a>
          </div>
        </div>
      </div>
    </div>
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
          :class="{'bg-orange':!$refs.audio.muted}"
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
      <div>
      <div v-if="isStart" class="h2 text-white font-weight-bold pt-5">GOGO~~~奮鬥下去!!</div>
      <div v-else class="h2 text-white font-weight-bold pt-5">休息是為了走更遠的路</div>
      <div class="h2 text-white font-weight-bold pt-5">{{ task }}</div>
      </div>
    </div>
    <!-- 固定下方的番茄圖 -->
    <div class="fixbottom d-flex justify-content-center">
      <img v-if="isStart" src="../assets/img/tomato--orange.svg" alt="" srcset="">
      <img v-else src="../assets/img/tomato--green.svg" alt="" srcset="">
    </div>
    <audio ref="audio" src="../assets/music/bell.mp3" type="audio/mpeg"></audio>
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
      setSecond: 10, // 初始共幾秒,
      minutes: '00', // 字幕顯示(分)
      seconds: '10', // 字幕顯示(秒)
      setminutes: '0',
      setseconds: '0',
      date: new Date(),
      interval: '',
      sidemenushow: false,
      newTodo: '',
      todos: [],
      visibility: 'all',
      task: '',
      timerun: false,
      caveat: '',
      clickindex: '',
      TimeCount: '',
    };
  },
  methods: {
    opencaveat() {
      $('.bd-example-modal-lg').modal('show');
    },
    play() {
      const vm = this;

      if (vm.task.length === 0) {
        vm.caveat = '請先選擇任務';
        vm.opencaveat();
      } else {
        if (vm.isStart) {
          clearInterval(vm.interval);
        } else {
          vm.interval = setInterval(() => {
            vm.counter();
          }, 1000);
          vm.timerun = true;
        }
        vm.isStart = !vm.isStart;
      }
    },
    clear() {
      const vm = this;
      clearInterval(vm.interval);
      vm.setSecond = vm.TimeCount[0].setSecond;
      vm.minutes = vm.TimeCount[0].minutes;
      vm.seconds = vm.TimeCount[0].seconds;
      vm.isStart = false;
      vm.task = '';
      vm.timerun = false;
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
        vm.todos[vm.clickindex].completed = true;
        localStorage.setItem('todos', JSON.stringify(vm.todos));
        vm.$refs.audio.play();
        vm.gettodos();
        vm.clear();
      }
    },
    menushow() {
      const vm = this;
      vm.sidemenushow = !vm.sidemenushow;
    },
    addTodo() {
      const vm = this;
      if (!vm.newTodo) {
        return;
      }
      vm.todos.push({
        id: Math.floor(Date.now()),
        title: vm.newTodo.trim(),
        completed: false,
      });
      localStorage.setItem('todos', JSON.stringify(vm.todos));
      vm.gettodos();
      vm.newTodo = '';
    },
    removeTodo(todo) {
      const vm = this;
      if (vm.timerun) {
        vm.caveat = '請先重置時間，才可以刪除任務。';
        vm.opencaveat();
      } else {
        const newIndex = vm.todos.findIndex((item) => todo.id === item.id);
        vm.todos.splice(newIndex, 1);
        localStorage.setItem('todos', JSON.stringify(vm.todos));
        vm.gettodos();
      }
    },
    deleteList() {
      const vm = this;
      if (vm.timerun) {
        vm.caveat = '請先重置時間，才可以清空所有任務。';
        vm.opencaveat();
      } else {
        localStorage.removeItem('todos');
        vm.todos = [];
        vm.gettodos();
      }
    },
    gettodos() {
      const vm = this;
      vm.todos = JSON.parse(localStorage.getItem('todos')) || [];
    },
    clickradio(todo) {
      const vm = this;
      const newIndex = vm.todos.findIndex((item) => todo.id === item.id);
      vm.clickindex = newIndex;
    },
    getTimeCount() {
      const vm = this;
      vm.TimeCount = JSON.parse(localStorage.getItem('TimeCount')) || [{
        setSecond: 1500,
        minutes: '25',
        seconds: '00',
      }];
      vm.setSecond = vm.TimeCount[0].setSecond;
      vm.minutes = vm.TimeCount[0].minutes;
      vm.seconds = vm.TimeCount[0].seconds;
    },
    setTimeCount() {
      const vm = this;
      if (vm.timerun) {
        vm.caveat = '請先重置時間，才可以設定到數時間。';
        vm.opencaveat();
      } else if ((vm.setminutes === '0' && vm.setseconds === '0') || (vm.setminutes > 60 || vm.setseconds > 60)) {
        vm.caveat = '請按照時間格式輸入';
        vm.opencaveat();
      } else {
        if (vm.setminutes < 10) {
          vm.setminutes = `0${vm.setminutes}`;
        }
        if (vm.setseconds < 10) {
          vm.setseconds = `0${vm.setseconds}`;
        }
        vm.minutes = vm.setminutes;
        vm.seconds = vm.setseconds;
        vm.setSecond = vm.setminutes * 60 + vm.setseconds * 1;
        vm.TimeCount = [{
          setSecond: vm.setSecond,
          minutes: vm.minutes,
          seconds: vm.seconds,
        }];
        localStorage.setItem('TimeCount', JSON.stringify(vm.TimeCount));
        vm.setminutes = '0';
        vm.setseconds = '0';
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
    filteredTodos() {
      const vm = this;
      if (vm.visibility === 'completed') {
        const newTodos = [];
        vm.todos.forEach((item) => {
          if (item.completed) {
            newTodos.push(item);
          }
        });
        return newTodos;
      } if (vm.visibility === 'all') {
        const newTodos = [];
        vm.todos.forEach((item) => {
          if (!item.completed) {
            newTodos.push(item);
          }
        });
        return newTodos;
      }
      return [];
    },
    checkcount() {
      const vm = this;
      if (vm.todos.length >= 1) {
        return true;
      }
      return false;
    },
  },
  created() {
    const vm = this;
    setInterval(() => {
      vm.date = new Date();
    }, 1000);
    vm.gettodos();
    vm.getTimeCount();
  },
};
</script>
