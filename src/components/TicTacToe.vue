<template>
  <div class="tictactoe-board">
        <cell :value="board[i][j]"></cell>
  </div>
</template>
<script>
  export default {
    data() { return {
      board: [
          ['', '', ''],
          ['', 'x', ''],
          ['', '', '']
        ]
        ,
        
    } },

    
  }
</script>
<style>
  .tictactoe-board {
    display: flex;
    flex-wrap: wrap;
    width: 204px;
    height: 204px;
  }
</style>