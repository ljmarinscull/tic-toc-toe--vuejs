<template>
<div>
  <div class="board" ref="board">
    <Cell class="cell" ref="cell" v-for="value in 9" :key="value" v-on:on-click="handleClick"/>
  </div>
  <div class="winning-message" ref="winningMessage">
    <div v-text="resultMsg" ></div>
    <button ref="restartButton" @click="resetAll">Restart</button>
  </div>
</div>
</template>

<script>
export default {
  components: {
    Cell: () => import("@/components/Cell"),
  },
  name: 'Board',
  data() {
    return {
      X_CLASS: 'x',
      CIRCLE_CLASS : 'o',
      WINNING_COMBINATIONS : [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
      ],
      circleTurn : false,
      resultMsg: ''
    }
} ,
  methods:{
    startGame(){
      this.circleTurn = false
  for(const cell in this.$refs.cell){
    cell.$el.classList.remove(this.X_CLASS)
    cell.$el.classList.remove(this.CIRCLE_CLASS)
  }
  this.setBoardHoverClass()
  this.winningMessageElement.classList.remove('show')
  },
  handleClick(e){
    const cell = e.target
    const currentClass = this.circleTurn ? this.CIRCLE_CLASS : this.X_CLASS
    this.placeMark(cell, currentClass)

    if(this.checkWin(currentClass)){
      this.endGame(false)
    }else if(this.isDraw()){
      this.endGame(true)
    } else {
      this.swapTurns()
      this.setBoardHoverClass()
    }
  },
  placeMark(cell, currentClass) {
    cell.classList.add(currentClass)
  },
  endGame(draw) {
  if (draw) {
    this.resultMsg = 'Draw!'
  } else {
    this.resultMsg = `${this.circleTurn ? "O's" : "X's"} Wins!`
  }
  this.$refs.winningMessage.classList.add('show')
  },
isDraw() {
  return [...this.$refs.cell].every(cell => {
    return cell.$el.classList.contains(this.X_CLASS) || cell.$el.classList.contains(this.CIRCLE_CLASS)
  })
},
swapTurns() {
  this.circleTurn = !this.circleTurn
},
setBoardHoverClass() {
  this.$refs.board.classList.remove(this.X_CLASS)
  this.$refs.board.classList.remove(this.CIRCLE_CLASS)
  if (this.circleTurn) {
    this.$refs.board.classList.add(this.CIRCLE_CLASS)
  } else {
    this.$refs.board.classList.add(this.X_CLASS)
  }
},
checkWin(currentClass) {
  return this.WINNING_COMBINATIONS.some(combination => {
    return combination.every(index => {
      return this.$refs.cell[index].$el.classList.contains(currentClass)
    })
  })},

  resetAll(){
    window.location.reload()
  }
}

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style >
:root{
  --cell-size: 100px;
  --mark-size: calc(var(--cell-size) * .9)
}

.board {
  width: 100vw;
  height: 100vh;
  display: grid;
  justify-content: center;
  align-content: center;
  justify-items: center;
  align-items: center;
  grid-template-columns: repeat(3, auto)
}

.cell {
  width: var(--cell-size);
  height: var(--cell-size);
  border: 1px solid black;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  cursor: pointer;
}

.cell:first-child,
.cell:nth-child(2),
.cell:nth-child(3) {
  border-top: none;
}

.cell:nth-child(3n + 1) {
  border-left: none;
}

.cell:nth-child(3n + 3) {
  border-right: none;
}

.cell:last-child,
.cell:nth-child(8),
.cell:nth-child(7) {
  border-bottom: none;
}

.cell.x,
.cell.o{
  cursor: not-allowed;
}

.cell.x::before,
.cell.x::after,
.cell.o::before {
  background-color: black;
}

.board.x .cell:not(.x):not(.o):hover::before,
.board.x .cell:not(.x):not(.o):hover::after,
.board.o .cell:not(.x):not(.o):hover::before {
  background-color: lightgrey;
}

.cell.x::before,
.cell.x::after,
.board.x .cell:not(.x):not(.o):hover::before,
.board.x .cell:not(.x):not(.o):hover::after {
  content: '';
  position: absolute;
  width: calc(var(--mark-size) * .15);
  height: var(--mark-size);
}

.cell.x::before,
.board.x .cell:not(.x):not(.o):hover::before {
  transform: rotate(45deg);
}

.cell.x::after,
.board.x .cell:not(.x):not(.o):hover::after {
  transform: rotate(-45deg);
}

.cell.o::before,
.cell.o::after,
.board.o .cell:not(.x):not(.o):hover::before,
.board.o .cell:not(.x):not(.o):hover::after {
  content: '';
  position: absolute;
  border-radius: 50%;
}

.cell.o::before,
.board.o .cell:not(.x):not(.o):hover::before {
  width: var(--mark-size);
  height: var(--mark-size);
}

.cell.o::after,
.board.o .cell:not(.x):not(.o):hover::after {
  width: calc(var(--mark-size) * .7);
  height: calc(var(--mark-size) * .7);
  background-color: white;
}

.winning-message {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, .9);
  justify-content: center;
  align-items: center;
  color: white;
  font-size: 5rem;
  flex-direction: column;
}

.winning-message button {
  font-size: 3rem;
  background-color: white;
  border: 1px solid black;
  padding: .25em .5em;
  cursor: pointer;
}

.winning-message button:hover {
  background-color: black;
  color: white;
  border-color: white;
}

.winning-message.show {
  display: flex;
}
</style>
