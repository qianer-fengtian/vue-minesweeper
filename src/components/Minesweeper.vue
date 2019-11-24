<template>
  <div>
    <div>
      <button class="btn" @click="beginner">
        🙈初級
      </button>
      <button class="btn" @click="intermediate">
        🙊中級
      </button>
      <button class="btn" @click="advanced">
        🙉上級
      </button>
    </div>
    <template v-if="started">
      <table class="board">
        <template v-for="(row, xi) in area">
          <tr :key="xi">
            <template v-for="(cell, yi) in row">
              <td
                :key="yi"
                class="cell"
                :class="{ 'cell--opened': cell.isOpen }"
                @click="open(xi, yi)"
                @click.right.prevent="mark(xi, yi)"
              >
                <span v-if="cell.isOpen">
                  {{ cell.isBomb ? "💣" : cell.bombCount === 0 ? "" : cell.bombCount }}
                </span>
                <span v-if="cell.isMark">
                  🚩
                </span>
              </td>
            </template>
          </tr>
        </template>    
      </table>
    </template>
  </div>
</template>

<script>
class Cell {
  isOpen = false;
  isBomb = false;
  isMark = false;
  bombCount = 0;
  openable() {
    return !this.isOpen && !this.isBomb;
  }
}

export default {
  name: "Minesweeper",

  data() {
    return {
      area: [],
      started: false,
      maxX: 0,
      maxY: 0,
      numOfBomb: 0,
    }
  },

  methods: {
    beginner() {
      this.maxX = 9;
      this.maxY = 9;
      this.numOfBomb = 10;
      this.start(this.maxX, this.maxY, this.numOfBomb);
    },

    intermediate() {
      this.maxX = 16;
      this.maxY = 16;
      this.numOfBomb = 40;
      this.start(this.maxX, this.maxY, this.numOfBomb);
    },

    advanced() {
      this.maxX = 16;
      this.maxY = 30;
      this.numOfBomb = 99;
      this.start(this.maxX, this.maxY, this.numOfBomb);
    },

    start(maxX, maxY, numOfbomb) {
      this.started = false;
      this.area = [];

      for (let i = 0; i < maxX; i++) {
        const row = [];
        for (let j = 0; j < maxY; j++) {
          row.push(new Cell());
        }
        this.area.push(row);
      }

      const getRandomInt = max => {
        return Math.floor(Math.random() * Math.floor(max));
      }

      for (let i = 0; i < numOfbomb; i++) {
        const x = getRandomInt(maxX);
        const y = getRandomInt(maxY);

        if (this.area[x][y].isBomb) {
          i--;
          continue;
        }

        this.area[x][y].isBomb = true;


        if (x-1 >= 0) {
          this.area[x-1][y].bombCount++;
          if (y-1 >= 0) this.area[x-1][y-1].bombCount++;
          if (y+1 < maxY) this.area[x-1][y+1].bombCount++;
        }

        if (y-1 >= 0) this.area[x][y-1].bombCount++;
        if (y+1 < maxY) this.area[x][y+1].bombCount++;

        if (x+1 < maxX) {
          this.area[x+1][y].bombCount++;
          if (y-1 >= 0) this.area[x+1][y-1].bombCount++;
          if (y+1 < maxY) this.area[x+1][y+1].bombCount++;
        }
      }

      this.started = true;
    },

    open(x, y) {
      if (this.area[x][y].isBomb) {
        alert("Bomb!!");
        for (let i = 0; i < this.maxX; i++) {
          for (let j = 0; j < this.maxY; j++) {
            this.area[i][j].isOpen = true;
          }
        }
      }

      this.area[x][y].isOpen = true;

      if (this.area[x][y].bombCount !== 0) return;

      if (x-1 >= 0 && this.area[x-1][y].openable()) {
        this.open(x-1, y);
      }
      if (x+1 <= 7 && this.area[x+1][y].openable()) {
        this.open(x+1, y);
      }
      if (y-1 >= 0 && this.area[x][y-1].openable()) {
        this.open(x, y-1);
      }
      if (y+1 <= 7 && this.area[x][y+1].openable()) {
        this.open(x, y+1);
      }
    },

    mark(x, y) {
      if (!this.area[x][y].isOpen) {
        this.area[x][y].isMark = !this.area[x][y].isMark;
      }
    }
  }
}
</script>

<style scoped>
.btn {
  background-color: #fd9535;
  border: double 3px #d27d00;
  border-radius: 8px;
  color: #eee;
  font-weight: bold;
  text-decoration: none;
  margin: 2px;
  width: 10rem;
}

table.board,
table.board td {
  border-spacing: 1px;
  border-color: #aaa;
  border-style: solid;
  border-width: 1px;
}

table.board {
  margin: 0 auto;
}

.cell {
  cursor: pointer;
  width: 30px;
  height: 30px;
  user-select: none;
}

.cell--opened {
  background-color: #e3e3e3;
}
</style>