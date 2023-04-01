<template>

  <div class="game-board">

    <div class="board">
      <h2>Ваша доска</h2>
      <div
          v-for="(row, rowIndex) in playerBoard"
          :key="rowIndex"
          class="row"
      >
        <div
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            class="cell"
            :class="{ 'cell-hit': cell === 2, 'cell-miss': cell === 1, ship: cell === 3}"
        >
          {{ cell === 1 ? 'X' : cell === 2 ? 'O' : '' }}
        </div>
      </div>
    </div>

    <div class="message" v-if="message">{{ message }}</div>

    <div class="board">
      <h2>Доска компьютера</h2>
      <div
          v-for="(row, rowIndex) in computerBoard"
          :key="rowIndex"
          class="row"
      >
        <div
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            class="cell"
            :class="{ 'cell-hit': cell === 2, 'cell-miss': cell === 1, ship: cell === 3}"
            @click="handlePlayerTurn(rowIndex, cellIndex)"
        >
          {{  cell === 1 ? 'X' : cell === 2 ? 'O' : '' }}
        </div>
      </div>
    </div>


  </div>

</template>

<script lang="ts">

import './css/app.css'
import {reactive, ref, onMounted} from "vue";

export default {
  name: 'App',
  setup()
  {

    const Board_Size: number = 10;

    let Ships = [
      {name: 'Авианосец',length: 5},
      {name: 'Боевой корабль',length: 4},
      {name: 'Субмарина',length: 3},
      {name: 'Шпион',length: 3},
      {name: 'Заправщик',length: 2},
    ];

    const playerBoard = reactive(Array(Board_Size).fill(null).map(()=>Array(Board_Size).fill(0)));
    const computerBoard = reactive(Array(Board_Size).fill(null).map(()=>Array(Board_Size).fill(0)));
    const shipsRemaining = ref(Ships.length);
    const message = ref('');

    const placeShip = (board: number[][], length: number):void => {
      const isVertical = Math.random() < 0.5;
      let x,y;

      if(isVertical)
      {
        if((Board_Size-length) >= 0) {
          x = Math.floor(Math.random() * (Board_Size - length));
          y = Math.floor(Math.random() * Board_Size);
          for(let i = 0; i < length; i++)
          {
            if(board[x+i][y] === 3){board[x+i][y+1] = 3;}
            else{board[x+i][y] = 3;}

            console.log('Vertical',x,y)
          }
        }
        else {
          x = Math.floor(Math.random() * (Board_Size + length));
          y = Math.floor(Math.random() * Board_Size);
          for(let i = 0; i < length; i++)
          {
            if(board[x-i][y] === 3){board[x-i+1][y+1] = 3;}
            else{board[x-i][y] = 3;}

            console.log('Vertical',x,y)
          }
        }
      }
      else
      {
        if((Board_Size-length) >= 0) {
          x = Math.floor(Math.random() * Board_Size);
          y = Math.floor(Math.random() * (Board_Size - length));
          for(let i = 0; i < length; i++)
          {
            if(board[x][y+i] === 3){board[x+1][y+i-1] = 3;}
            else{board[x][y+i] = 3;}

            console.log('Horizontal',x,y)
          }
        }
        else
        {
          x = Math.floor(Math.random() * Board_Size);
          y = Math.floor(Math.random() * (Board_Size + length));
          for(let i = 0; i < length; i++)
          {
            if(board[x][y-i] === 3){board[x+1][y-i+1] = 3;}
            else{board[x][y-i] = 3;}

            console.log('Horizontal',x,y)
          }
        }
      }
    }

    const placeAllShips = (board: number[][]):void => {
      for(const ship of Ships)
      {
        placeShip(board,ship.length)
      }
    }

    const handlePlayerTurn = (row: number, col:number):void => {
      if(computerBoard[row][col] === 2)
      {
        message.value = 'Вы уже стреляли в эту клетку';
      }
      else if(computerBoard[row][col] === 1)
      {
        message.value = 'Вы уже стреляли в эту клетку';
      }
      else {
        if(computerBoard[row][col] === 0)
        {
          computerBoard[row][col] = 1;
          message.value = 'Промах';
          handleComputerTurn();
        }
        else
        {
          computerBoard[row][col] = 2;
          shipsRemaining.value--;
          message.value = 'Попадание';
          if(shipsRemaining.value === 0)
          {
            message.value = 'Победа ваша!';
          }
        }
      }
    }

    const handleComputerTurn = ():void => {
      let row, col;
      do {
        row = Math.floor(Math.random() * Board_Size);
        col = Math.floor(Math.random() * Board_Size);
      } while(playerBoard[row][col] === 2);

      if(playerBoard[row][col] === 0)
      {
        playerBoard[row][col] = 1;
        message.value = 'Компьютер промахнулся!';
      } else {
        playerBoard[row][col] = 2;
        message.value = 'Компьютер попал в ваш корабль!';
        handleComputerTurn();
      }
    }

    onMounted(() => {
      placeAllShips(computerBoard);
      placeAllShips(playerBoard)
    })

    return {
      handleComputerTurn,
      handlePlayerTurn,
      playerBoard,
      computerBoard,
      message,
    }
  }
}


</script>

<style scoped>
.game-board {
  position: absolute;
  display: flex;
  justify-self: center;
  align-self: center;
  text-align: center;
}

.board {
  justify-self: center;
  align-self: start;
  padding: 25px;
  text-align: center;
}

.row {
  display: flex;
  flex-direction: row;
}

.cell {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 40px;
  height: 40px;
  border: 1px solid black;
  margin: 1px;
  cursor: pointer;
  background-color: white;
}

.ship
{
  background-color: aqua;
}

.cell-hit {
  background-color: #f44336;
}

.cell-miss {
  background-color: #b0bec5;
}

.player-board {
  margin-right: 20px;
}

.message {
  margin-top: 20px;
  font-weight: bold;
  justify-self: center;
}
</style>
