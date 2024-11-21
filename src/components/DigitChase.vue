<template>
    <div class="game">
        <h1>数位追踪</h1>
        <h2>Digit Chase</h2>
        <p>系统已生成四位互不相同的非零数字<br>尝试在10次尝试内猜中！</p>
        <p>A - 数字和位置均正确的个数<br>B - 数字正确但位置不对的个数</p>

        <div v-if="!gameOver">
            <input v-for="(value, index) in guess" :key="index" type="text" min="1" max="9" v-model="guess[index]"
                maxlength="1" @input="handleInput(index)" ref="inputs" @keydown.enter="handleEnter(index)"
                @keydown.backspace="handleBackspace(index)" />
        </div>

        <div v-else>
            <p v-if="gameWon" style="color: greenyellow;">恭喜你猜中了！</p>
            <p v-else style="color: crimson;">很遗憾，游戏结束！</p>
        </div>

        <button @click="restartGame" class="reset">重新开始</button>

        <button v-if="!gameOver" @click="submitGuess" class="commit">提交猜测</button>

        <ul>
            <li v-for="(attempt, index) in attempts" :key="index">
                <strong>猜测{{ ['一', '二', '三', '四', '五', '六', '七', '八', '九', '十'][index]
                    }}</strong>&nbsp;&nbsp;&nbsp;&nbsp;{{
                        attempt.guess.join(' &nbsp;') }} &nbsp;- &nbsp;{{ attempt.A }}A {{ attempt.B }}B
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            guess: [null, null, null, null],
            target: [],
            attempts: [],
            gameOver: false,
            gameWon: false
        };
    },
    mounted() {
        this.startNewGame();
    },
    methods: {
        startNewGame() {
            this.target = this.generateTarget();
            this.attempts = [];
            this.gameOver = false;
            this.gameWon = false;
            this.guess = [null, null, null, null];
        },
        generateTarget() {
            const numbers = [];
            while (numbers.length < 4) {
                const num = Math.floor(Math.random() * 9) + 1;
                if (!numbers.includes(num)) {
                    numbers.push(num);
                }
            }
            console.debug(`Answer: ${numbers.join(' ')}`);
            return numbers;
        },
        submitGuess() {
            const currentGuess = [];
            for (let guess of this.guess) {
                if (['1','2','3','4','5','6','7','8','9'].includes(guess)) currentGuess.push(parseInt(guess));
                else {
                    alert('请输入四个不同的数字！');
                    return;
                }
            }
            if (new Set(currentGuess).size !== 4) {
                alert('请输入四个不同的数字！');
                return;
            }

            let A = 0;
            let B = 0;

            currentGuess.forEach((num, index) => {
                if (num === this.target[index]) {
                    A++;
                } else if (this.target.includes(num)) {
                    B++;
                }
            });

            this.attempts.push({ guess: currentGuess, A, B });

            if (A === 4) {
                this.gameWon = true;
                this.gameOver = true;
            } else if (this.attempts.length >= 10) {
                this.gameOver = true;
            }

            this.guess = [null, null, null, null];
            this.$refs.inputs[0].focus();
        },
        handleInput(index) {
            const value = this.guess[index];
            if (!['1','2','3','4','5','6','7','8','9'].includes(value)) {
                this.guess[index] = null;
            } else if (value && index < this.guess.length - 1) {
                this.$refs.inputs[index + 1].focus();
            }
        },
        handleBackspace(index) {
            if (!this.guess[index]) {
                if (index > 0) {
                    this.$refs.inputs[index - 1].focus();
                }
            }
        },
        handleEnter(index) {
            if (index === this.guess.length - 1 && this.guess[index] !== null) {
                this.submitGuess();
            } else {
                this.$refs.inputs[index + 1].focus();
            }
        },
        restartGame() {
            this.startNewGame();
        }
    }
}

</script>

<style scoped>
.game {
    max-width: 500px;
    margin: 0 auto;
    text-align: center;
    font-family: 'Arial', sans-serif;
}

h1 {
    color: #4a90e2;
    margin-bottom: 20px;
}

h2 {
    color: #3869a1;
    margin-bottom: 20px;
}

input[type='number'] {
    -webkit-appearance: none;
    -moz-appearance: textfield;
    appearance: textfield;
}

input[type='number']::-webkit-inner-spin-button,
input[type='number']::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

input {
    width: 60px;
    height: 60px;
    margin: 5px;
    text-align: center;
    font-size: 2.5rem;
    border: 2px solid #4a90e2;
    border-radius: 10px;
    outline: none;
    background-color: #f7f9fc;
    color: #333;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease;
}

input:focus {
    border-color: #ff6b6b;
    box-shadow: 0 6px 10px rgba(0, 0, 0, 0.15);
    background-color: #ffffff;
}

button {
    padding: 10px 20px;
    margin: 10px;
    font-size: 1.4rem;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.commit {
    background-color: #4a90e2;
}

.reset {
    background-color: #d23f3f;
}

.commit:hover {
    background-color: #357ab8;
}

.reset:hover {
    background-color: #a83d3d;
}

ul {
    list-style-type: none;
    padding: 0;
    margin: 20px 0;
}

li {
    margin: 10px 0;
}

p {
    font-size: 1.1rem;
    color: #999;
}

</style>
