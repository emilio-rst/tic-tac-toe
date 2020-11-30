<template>
  <div id="app">
    <div class="status" v-if="status == statuses.NEXT_PLAYER_X">Next player X</div>
    <div class="status" v-if="status == statuses.NEXT_PLAYER_O">Next player O</div>
    <div class="status" v-if="status == statuses.WINNER_X">Winner player X</div>
    <div class="status" v-if="status == statuses.WINNER_O">Winner player O</div>

    <button class="reset" @click="reset">Reset</button>

    <template v-for="row in 3">
      <div class="row" :key="row">
        <button v-for="button in 3" @click="fillBox(row-1, button-1)" class="square">
          <span v-if="getBoxValue(row-1, button-1) == player.X">X</span>
          <span v-if="getBoxValue(row-1, button-1) == player.O">O</span>
          <span v-else>&nbsp;</span>
        </button>
      </div>
    </template>
  </div>
</template>

<script>
  /**
   * Statuses of the game
   */
  const statuses = {
    NEXT_PLAYER_X: 1,
    NEXT_PLAYER_O: 2,
    WINNER_X: 3,
    WINNER_O: 4,
    DRAWN: 5,
  }

  /**
   * Players of the game
   */
  const player = {
    X: 1,
    O: 2,
  }

  /**
   * Board
   */
  class Board {

    constructor() {
      this.reset()
      this.status = null
    }

    /**
     * Reset the board
     */
    reset() {
      this.grid = [
        [null, null, null,],
        [null, null, null,],
        [null, null, null,],
      ]
      this.status = null
      this.count = 0
    }

    /**
     * Set X value to the box
     */
    setX(x, y) {
      if(this.grid[x][y]) {
        return false
      }

      this.grid[x][y] = player.X
      this.count++

      return true
    }

    /**
     * Set O value to the box
     */
    setO(x, y) {
      if(this.grid[x][y]) {
        return false
      }

      this.grid[x][y] = player.O
      this.count++

      return true
    }

    /**
     * Get box value
     */
    getValue(x, y) {
      return this.grid[x][y]
    }

    /**
     * Get status of the board
     */
    getStatus() {

      if(!this.status) {
        if(this.hasWin(player.X)) {
          this.status = statuses.WINNER_X
        } else if(this.hasWin(player.O)) {
          this.status = statuses.WINNER_O
        } else if(this.count == 9) {
          this.status = statuses.DRAWN
        }
      }

      return this.status
    }

    /**
     * Has win the player?
     */
    hasWin(playerValue) {
      let i = 0

      for(i=0; i<3; i++) {
        if(this.grid[i][0] == playerValue && this.grid[i][1] == playerValue && this.grid[i][2] == playerValue) {
          return true
        }
      }

      for(i=0; i<3; i++) {
        if(this.grid[0][i] == playerValue && this.grid[1][i] == playerValue && this.grid[2][i] == playerValue) {
          return true
        }
      }

      if(this.grid[1][1] == playerValue) {
        if(this.grid[0][0] == playerValue && this.grid[2][2] == playerValue) {
          return true
        }
        if(this.grid[0][2] == playerValue && this.grid[2][0] == playerValue) {
          return true
        }
      }

      return false
    }
  }

  export default {
    name: "App",
    data() {
      return {
        status: statuses.NEXT_PLAYER_X,
        statuses,
        board: new Board(),
        player,
      }
    },

    methods: {
      /**
       * Reset the game
       */
      reset() {
        this.board.reset()
        this.status = statuses.NEXT_PLAYER_X
      },

      /**
       * Fill the box
       */
      fillBox(x, y) {

        if(this.status == statuses.NEXT_PLAYER_X) {
          if(!this.board.setX(x, y)) {
            return false
          }

          let status = this.board.getStatus()
          if(status) {
            this.status = status
          } else {
            this.status = statuses.NEXT_PLAYER_O
          }

        } else if(this.status == statuses.NEXT_PLAYER_O) {
          if(!this.board.setO(x, y)) {
            return false
          }

          let status = this.board.getStatus()
          if(status) {
            this.status = status
          } else {
            this.status = statuses.NEXT_PLAYER_X
          }
        }
      },

      getBoxValue(x, y) {
        return this.board.getValue(x, y)
      }
    }
  }
</script>


<style lang="scss">
  button.square {
    width:40px;
    height:40px;
  }

  button.reset {
    margin-bottom: 20px;
    margin-top: 10px;
  }
</style>
