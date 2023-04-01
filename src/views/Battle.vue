<template>

  <div class="board">
    <div class="row" v-for="(row, rowIndex) in grid" :key="rowIndex">
      <div
          class="cell"
          v-for="(cell, colIndex) in row"
          :key="colIndex"
          :class="{ empty: cell === CellState.EMPTY, ship: cell === CellState.SHIP, hit: cell === CellState.HIT, miss: cell === CellState.MISS }"
          @click="handleCheck(rowIndex, colIndex)"
      ></div>
    </div>
    <div v-if="gameOver.state" class="game-over">Game Over</div>
  </div>

  <RouterView />
</template>

<script lang="ts">

import { RouterLink, RouterView } from 'vue-router'
import './css/app.css'
import {reactive, ref} from "vue";

export default {
  name: 'App',
  setup()
  {
    enum BoardState
    {
      PLAYING,
      GAME_OVER
    }

    const CellState = ref({
      EMPTY: 0,
      SHIP: 1,
      HIT: 2,
      MISS: 3,
    })

    const grid = reactive(Array(10)
        .fill(null).map(()=>Array(10)
            .fill(null).map(()=> Math.round(Math.random()))));

    console.log(grid);
    const gameState = ref(BoardState.PLAYING);
    const gameOver = reactive({state: false});

    const handleCheck = (row: number, col: number) => {
      if(gameOver.state)
      {
        return;
      }
      if(grid[row][col] === CellState.value.EMPTY){
        grid[row][col] = CellState.value.MISS;
      }
      else if (grid[row][col] === CellState.value.HIT || grid[row][col] === CellState.value.MISS)
      {
        return;
      }
      else {
        grid[row][col] = CellState.value.HIT;
        const count = grid.reduce((acc: number,row: any) => acc + row.filter((cell: number) => cell === CellState.value.SHIP).length, 0);
        console.log(count);
        if (count === 0) {
          gameOver.state = true;
          gameState.value = BoardState.GAME_OVER;
        }
      }
    }

    return{
      grid,
      handleCheck,
      CellState,
      gameOver
    }
  }
}


</script>

<style scoped>
.board {
  display: flex;
  flex-direction: column;
}
.row {
  display: flex;
  flex-direction: row;
}
.cell {
  width: 40px;
  height: 40px;
  border: 1px solid black;
  margin: 1px;
  cursor: pointer;
}
.empty {
  background-color: white;
}
.ship {
  background-color: white;
}
.hit {
  background-color: red;
}
.miss {
  background-color: blue;
}
.game-over {
  font-size: 24px;
  font-weight: bold;
  text-align: center;
  margin-top: 20px;
}

</style>
