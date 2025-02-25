<template>
    <div class="memory-game">
        <h1>{{ header }}</h1>
        <div class="game-container">
            <div class="row" v-for="(cardRow, rIndex) in cardContainer" :key="'row' + rIndex">
                <div v-for="(card, cIndex) in cardRow">
                    <div class="card" :class="{ active: card.status }">
                        <div class="card-front" v-on:click="selectCard(rIndex, cIndex)"></div>
                        <div class="card-back">{{ (card.status === true ? card.value : "") }}</div>
                    </div>
                </div>

            </div>
        </div>
        <div v-if="isGameSet">
            <button class="restart-btn" v-on:click="reset">Restart</button>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: "MemoryGame",
    data() {
        return {
            header: "Memory game (Vue)",
            cardContainer: [
                [{ value: "A", status: false }, { value: "B", status: false }, { value: "B", status: false }, { value: "F", status: false }],
                [{ value: "F", status: false }, { value: "A", status: false }, { value: "C", status: false }, { value: "E", status: false }],
                [{ value: "G", status: false }, { value: "D", status: false }, { value: "G", status: false }, { value: "E", status: false }],
                [{ value: "H", status: false }, { value: "C", status: false }, { value: "H", status: false }, { value: "D", status: false }]
            ],
            selectedPrev: { row: <number | undefined>undefined, col: <number | undefined>undefined },
            isSelectCardFunctionRunning: <boolean>false,
            isGameSet: <boolean>false
        }
    }, methods: {
        selectCard(row: number, col: number) {
            // return if animation not end
            if (this.isSelectCardFunctionRunning) return
            // open card
            const selectedPrev = this.selectedPrev;
            this.cardContainer[row][col].status = true;
            // check prev card
            if (selectedPrev.row !== undefined && selectedPrev.col !== undefined) {
                // selected
                const prevValue = this.cardContainer[selectedPrev.row][selectedPrev.col].value;
                const curValue = this.cardContainer[row][col].value;

                if (prevValue !== curValue) {
                    // not same && remove current Previous selection
                    this.isSelectCardFunctionRunning = true;
                    setTimeout(() => {
                        this.cardContainer[row][col].status = false;
                        this.cardContainer[selectedPrev.row!][selectedPrev.col!].status = false;
                        this.isSelectCardFunctionRunning = false;
                    }, 750)
                }

                this.selectedPrev = {
                    row: undefined,
                    col: undefined
                }
            } else if (this.selectedPrev.row === undefined && this.selectedPrev.col === undefined) {
                // not selected
                this.selectedPrev = {
                    row: row,
                    col: col
                }
            }

            if (this.cardContainer.every(item => item.every(subItem => subItem.status === true))) {
                setTimeout(() => {
                    this.isGameSet = true;
                    alert("You Won !!!");
                }, 1000)
            }
        },
        shuffleArray(arr: { value: string, status: boolean }[][]) {
            for (let i = 0; i < arr.length; i++) {
                for (let j = 0; j < arr[i].length; j++) {
                    const row = Math.floor(Math.random() * arr.length);
                    const col = Math.floor(Math.random() * arr[i].length);

                    const temp = arr[i][j];
                    arr[i][j] = arr[row][col];
                    arr[row][col] = temp;
                }
            }
        },
        reset() {
            this.cardContainer.forEach(item => item.forEach(subItem => subItem.status = false));
            this.shuffleArray(this.cardContainer);
            this.isGameSet = false;
        }
    }, beforeMount() {
        this.shuffleArray(this.cardContainer);
    }
}



</script>

<style scoped>
.memory-game {
    perspective: 100px;
}

.row {
    display: flex;
    gap: 15px;
    margin-top: 15px;
}

.card {
    position: relative;
    font-size: 25px;
    font-weight: bold;
    width: 35px;
    height: 35px;
    transition-duration: 0.5s;
    transform-style: preserve-3d;
}

.card-front,
.card-back {
    border-radius: 20px;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
}

.card-front {
    background-color: black;
    color: white;
}

.card-back {
    background-color: white;
    color: black;
    border: 2px solid black;
    width: 100%;
    height: 100%;
    transform: rotateY(180deg);
}

.active {
    transform: rotateY(180deg);
}

.restart-btn {
    margin-top: 35px;
    background-color: #363533;
    color: white;
}
</style>
