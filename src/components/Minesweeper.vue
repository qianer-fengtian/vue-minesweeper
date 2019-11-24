<template>
  <div>
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
              <template v-if="cell.isOpen && cell.isBomb">
                💣
              </template>
              <template v-if="cell.isMark">
                🚩
              </template>
              <template v-if="cell.isOpen && !cell.isBomb">
                {{ cell.bombCount }}
              </template>
            </td>
          </template>
        </tr>
      </template>
    </table>
  </div>
</template>

<script>
class Cell {
  isOpen = false;
  isBomb = false;
  isMark = false;
  bombCount = 0;

  opens() {
    return !this.isOpen && !this.isBomb;
  }
}

export default {
  name: "Minesweeper",

  data() {
    return {
      bombs: 10,
      area: []
    }
  },

  created() {
    for (let i = 0; i < 8; i++) {
      const row = [];
      for (let j = 0; j < 8; j++) {
        row.push(new Cell());
      }
      this.area.push(row);
    }

    const getRandomInt = max => {
      return Math.floor(Math.random() * Math.floor(max));
    }

    for (let i = 0; i < 10; i++) {
      const x = getRandomInt(8);
      const y = getRandomInt(8);
      this.area[x][y].isBomb = true;
      if (x > 0) this.area[x-1][y].bombCount++;
      if (x < 7) this.area[x+1][y].bombCount++;
      if (y > 0) this.area[x][y-1].bombCount++;
      if (x > 0 && y > 0) this.area[x-1][y-1].bombCount++;
      if (x < 7 && y > 0) this.area[x+1][y-1].bombCount++;
      if (y < 7) this.area[x][y+1].bombCount++;
      if (x > 0 && y < 7) this.area[x-1][y+1].bombCount++;
      if (x < 7 && y < 7) this.area[x+1][y+1].bombCount++;
    }
  },

  methods: {
    open(x, y) {
      if (this.area[x][y].isBomb) {
        alert("Bomb!!");
        for (let i = 0; i < 8; i++) {
          for (let j = 0; j < 8; j++) {
            this.area[i][j].isOpen = true;
          }
        }
      }

      this.area[x][y].isOpen = true;

      if (this.area[x][y].bombCount !== 0) {
        return;
      }

      if (x-1 >= 0 && this.area[x-1][y].opens()) {
        this.open(x-1, y);
      }
      if (x+1 <= 7 && this.area[x+1][y].opens()) {
        this.open(x+1, y);
      }
      if (y-1 >= 0 && this.area[x][y-1].opens()) {
        this.open(x, y-1);
      }
      if (y+1 <= 7 && this.area[x][y+1].opens()) {
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
  width: 50px;
  height: 50px;
  user-select: none;
}

.cell--opened {
  background-color: #e3e3e3;
}
</style>