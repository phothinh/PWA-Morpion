<script>
    export default {
    data() {
        return{
            board:[
                ['', '', ''],
                ['', '', ''],
                ['', '', '']
            ],
            player:['Player X', 'Player O'],
            currentPlayerIndex: 0,
        }
    },
    computed: {
        currentPlayer() {
        return this.player[this.currentPlayerIndex]
        },
        winner() {
            const lines = [
                [[0, 0], [0, 1], [0, 2]], //ligne 0
                [[1, 0], [1, 1], [1, 2]], //ligne 1
                [[2, 0], [2, 1], [2, 2]], //ligne 2
                [[0, 0], [1, 0], [2, 0]], //col 0
                [[0, 1], [1, 1], [2, 1]], //col 1
                [[0, 2], [1, 2], [2, 2]], //col 2
                [[0, 0], [1, 1], [2, 2]], //diag 0
                [[0, 2], [1, 1], [2, 0]], //diag 1
            ]
            for (let line of lines) {
                const [a, b, c] = line
                if ( this.board[a[0]][a[1]] && this.board[a[0]][a[1]] === this.board[b[0]][b[1]] && this.board[a[0]][a[1]] === this.board[c[0]][c[1]]) {
                    return this.currentPlayer
                }
            }
            return null
        },
        isTie() {
            return this.board.every((row) => row.every((square) => square))
        },
        gameOver() {
            return !!this.winner || this.isTie
        }
    },
    methods: {
        selectSquare(row, col) {
            if (this.gameOver || this.board[row][col]) {
                return
            }

            this.board[row].splice(col, 1, this.currentPlayerIndex === 0 ? 'X' : 'O')
            const winner = this.winner

            if (winner) {
                console.log(winner + ' wins!')
            } else if (this.isTie) {
                console.log('It\'s a tie!')
            } else {
                this.currentPlayerIndex = 1 - this.currentPlayerIndex
            }
            console.log(row, col)
        },
        resetGame() {
            this.board = [
            ['', '', ''],
            ['', '', ''],
            ['', '', ''],
            ]
        }
    }
    
    }
</script>

<template>
    <div>
        <div v-if="winner">{{ winner }} wins!</div>
        <div v-else-if="isTie">It's a tie!</div>
        <div v-else>Current player: {{ currentPlayer }}</div>
        <button v-if="gameOver" @click="resetGame()">Play Again</button>

        <table>
            <tr v-for="(row, rowIndex) in board" :key="rowIndex" >
                <th v-for="(square, colIndex) in row" :key="colIndex" @click="selectSquare(rowIndex, colIndex)">
                    {{ square }}
                </th>
            </tr>
        </table>

  </div>
</template>

<style scoped>
th{
    border: 1px solid black;
    width: 60px;
    height: 60px;
}

table{
    margin-top:10px;
}
</style>