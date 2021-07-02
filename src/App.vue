<template>
      <div class='container'>
        <div class='circle'>
            <div 
            v-for="(item, i) in parts" 
            v-bind:key="item" 
            :class="[item, {disabled: disabledParts}, {lighter: showActivePart(i)}]" 
            @click='handleClick' 
            style="color: white; text-align: center; padding-top: 40px"
            :data-index=i>
            </div>
        </div>
        <div class='game-info'>
            <p><b>Round:</b><span class='round'> {{ round }} </span></p>
            <button v-bind:disabled='disabledBtn' @click='startGame' class='start'>Start</button>
            <p v-show='lost' class='lost-message'>Sorry, you lost after <span class='round'>{{ round }}</span> rounds</p>
        </div>
        <div @change='changeLevel' class='game-levels'>
            <h3>Game Levels</h3>
            <p><input type='radio' name='level' class='light' value='1500'  checked>Лёгкий</p>
            <p><input type='radio' name='level' class='normal' value='1000'>Средний</p>
            <p><input type='radio' name='level' class='hard' value='400'>Сложный</p>
        </div>
    </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      round: 0,
      arr: [],
      hasClass: [],
      sound: '',
      disabledBtn: false,
      disabledParts: true,
      lost: false,
      soundNumber: 1,
      btnClick: 0,
      partsClick: 0,
      partsIndex: 0,
      parts: ['red', 'green', 'blue', 'yellow'],
      activePartIndex: 5,
      time: 1500,
      timeDelay: 1
    };
  },
  methods: {
    changeLevel(event) {
        this.time = Number(event.target.value);
        this.round = 1;
        this.arr = [];
    },
    showActivePart(ind) { 
        if (this.activePartIndex < 5) {
          setTimeout(() => {
            // to remove class
            this.activePartIndex = 5;
            this.disabledBtn = false;
            this.disabledParts = false;
        }, this.time * this.timeDelay); //1500
        }
        return ind === this.activePartIndex;
    },
    startGame() {
      this.lost = false;
      this.btnClick++;
      if (this.btnClick > 1) {
        return;
      }
      this.disabledBtn = true;
      this.round = 1;
      this.showPart(1);
    },
    renderRandom() {
      return Math.floor(Math.random() * 4);
    },
    renderSound(number) {
      this.sound = new Audio(`sounds/sound${number}.mp3`);
    },
    handleClick(event){
      // cant click before parts are shown
      if (this.disabledBtn || this.disabledParts) {
        return
      }
      this.renderSound(Number(event.target.dataset.index) + 1);
      this.sound.play();
      event.target.classList.add('lighter');
      this.hasClass.push(event.target);
      if (this.hasClass.length > 1) {
        this.hasClass[0].classList.remove('lighter');
        this.hasClass.shift(0);
      }

      // если игрок ошибся
      if (this.arr[this.partsClick] !== Number(event.target.dataset.index)) {
        this.lost = true;
        this.partsClick = 0;
        this.btnClick = 0;
        setTimeout(() => {
            event.target.classList.remove('lighter');
            this.arr = [];
        }, 200);
        
      } else {
        event.target.classList.add('lighter');
        this.partsClick++;
        if (this.partsClick === this.arr.length) {
          setTimeout(() => {
            event.target.classList.remove('lighter');
          }, 200);
          this.round++;
          this.arr = [];
          this.partsClick = 0;
          setTimeout(() => {
            this.showPart(this.round);
          }, 1500) 
        }       
      }
    },
    showPart() {
      this.disabledBtn = true;
      for (let i = 0; i < this.round; i++) {
        this.arr.push(this.renderRandom());
      }
      // чтобы в массив добавлялось не больше 1 элемента 
      if (this.arr.length > this.round) {
        this.arr =  this.arr.slice(0, this.round);
      }
      this.arr.forEach((item, index) => {        
        setTimeout(() => {
          // если последний элемент массива
            if (index === this.arr.length - 1) {
                this.disabledBtn = false;
                this.disabledParts = false;
                this.btnClick = 0;
            }
            this.timeDelay = (index + 1) * this.arr.length;
            this.activePartIndex = item;

            this.renderSound(item + 1);
            this.sound.play();
            // если элементы дублируются, то будет эффект мигания прозрачности
            if (this.arr.length > 0 || index + 1 <= this.arr.length) {
              if (this.arr[index] === this.arr[index - 1] || this.arr[index] === this.arr[index + 1]) {
                this.activePartIndex = 5;
                setTimeout(() => {
                  this.activePartIndex = item;
                }, 300);
              }
            }
        }, this.time * index)
      });
    }
  }
}
</script>

<style src='./style.css'></style>
