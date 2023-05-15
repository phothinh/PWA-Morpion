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
            deferredPrompt: null
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
    mounted() {
        window.addEventListener('beforeinstallprompt', (event) => {
        event.preventDefault();
        this.deferredPrompt = event;
        });
    },
    methods: {
        async sendNotification() {
            const notification = document.querySelector("#notification");
            const registration = await navigator.serviceWorker.getRegistration();

            if (Notification.permission === "granted") {
                this.showNotification(notification.value, registration);
            } else {
                if (Notification.permission !== "denied") {
                const permission = await Notification.requestPermission();

                if (permission === "granted") {
                    this.showNotification(notification.value, registration);
                }
                }
            }
            },
            showNotification(body, registration) {
            const title = "What PWA Can Do Today";

            const payload = {
                body,
            };

            if ("showNotification" in registration) {
                registration.showNotification(title, payload);
            } else {
                new Notification(title, payload);
            }
        },
        speakResult(result) {
            const utterance = new SpeechSynthesisUtterance(result === 'tie' ? 'It is a tie!' : `${result} wins!`);
            speechSynthesis.speak(utterance);
        },

        installApp() {
            if (this.deferredPrompt) {
                this.deferredPrompt.prompt();
                this.deferredPrompt.userChoice.then((choiceResult) => {
                if (choiceResult.outcome === 'accepted') {
                    console.log('L\'application a été installée');
                } else {
                    console.log('L\'application n\'a pas été installée');
                }
                this.deferredPrompt = null;
                });
            }
        },
        
        selectSquare(row, col) {
        if (this.gameOver || this.board[row][col]) {
            return
        }

        this.board[row].splice(col, 1, this.currentPlayerIndex === 0 ? 'X' : 'O')
        const winner = this.winner

        if (winner) {
            const title = 'Tic-Tac-Toe';
            const options = {
                body: winner + ' wins!',
                icon: '/path/to/icon.png'
            };
            navigator.serviceWorker.ready.then(function(registration) {
                registration.showNotification(title, options);
                this.sendNotification();
                this.speakResult(winner);
            });
        } else if (this.isTie) {
            const title = 'Tic-Tac-Toe';
            const options = {
                body: 'It\'s a tie!',
                icon: '/path/to/icon.png'
            };
            navigator.serviceWorker.ready.then(function(registration) {
                registration.showNotification(title, options);
                this.sendNotification();
                this.speakResult('tie');
            });
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
    <div class="contain">
        <div v-if="winner">{{ winner }} wins!</div>
        <div v-else-if="isTie">It's a tie!</div>
        <div v-else>Current player: {{ currentPlayer }}</div>

        <table>
            <tr v-for="(row, rowIndex) in board" :key="rowIndex" >
                <th v-for="(square, colIndex) in row" :key="colIndex" @click="selectSquare(rowIndex, colIndex)">
                    {{ square }}
                </th>
            </tr>
        </table>

        <button v-if="gameOver" @click="resetGame()">Play Again</button>
        <button v-if="deferredPrompt" @click="installApp">Installer l'application</button>

  </div>
</template>

<style scoped>
th{
    border: 1px solid black;
    width: 10vw;
    height: 10vw;
}

.contain{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

table{
    margin-top:10vh;
}

button{
    margin-top:10px;
}
</style>