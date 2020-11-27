<template>
  <div>
    <div class="heading">
      <h1>Tic-Tac-Toe</h1>
    </div>
    <div class="tictactoe-board">
      <div v-for="(n, i) in 3">
        <div v-for="(n, j) in 3">
          <cell @click="performMove(j, i)"
                :value="board.cells[j][i]"
          />
        </div>
      </div>

      <div class="game-over-text" v-if="gameOver">
        {{ gameOverText }}
      </div>
    </div>
  </div>
</template>
<script>
  import Board from "./Board";

  export default {

    data() { return {
      gameOver: false,
      gameOverText: '',
      board: new Board()
    } },

    methods: {
      performMove(x, y) {
        if (this.gameOver) {
          return;
        }

        if (! this.board.doMove(x, y, 'x')) {
          // movimiento invalido
          return;
        }

        this.$forceUpdate();

        if (this.board.isGameOver()) {
          this.gameOver = true;
          this.gameOverText = this.board.playerHas3InARow('x') ? 'Ganaste!' : 'empate';
          return;
        }

        let aiMove = this.minimax(this.board.clone(), 'o');
        this.board.doMove(aiMove.move.x, aiMove.move.y, 'o');

        if (this.board.isGameOver()) {
          this.gameOver = true;
          this.gameOverText = this.board.playerHas3InARow('o') ? 'Perdiste!' : 'Empate';
        }

        this.$forceUpdate();
      },

      minimax(board, player, depth = 1) {

        // Si la tabla tiene 3 en una fila, devuelve la puntuación de la tabla.
        if (board.isGameOver()) {
          return {
            score: board.getScore() + depth,
            move: null
          }
        }

        // El jugador "o" quiere maximizar su puntuación, el jugador
        // "x" quiere minimizar su puntuación.
        let bestScore = player === 'o' ? -10000 : 10000;
        let bestMove = null;

        let moves = board.getPossibleMoves();
        for (let i = 0; i < moves.length; i++) {
          let move = moves[i];
          let newBoard = board.clone();
          newBoard.doMove(move.x, move.y, player);

          // Recurrir a la función minimax para el nuevo tablero.
          const score = this.minimax(newBoard, player === 'x' ? 'o' : 'x', depth + 1).score;

          // Si el puntaje es mejor que el mejor guardado, actualiza el puntaje del
          // mejor guardado a este movimiento.
          if ((player === 'o' && score > bestScore) || (player === 'x' && score < bestScore)) {
            bestScore = score;
            bestMove = move;
          }
        }

        //Devuelve la mejor puntuación y movimiento
        return {
          score: bestScore,
          move: bestMove
        }
      }
    }

  }
</script>
<style>
  .tictactoe-board {
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;

    width: 204px;
    height: 204px;
  }

  .game-over-text {
    font-weight: bold;
    color: rgb(224, 224, 224);
    width: 204px;
    font-size: 16px;
    text-align: center;
    padding-top: 12px;
  }

  .heading {
    text-align: center;
    width: 320px;
    color: rgb(231, 231, 231);
  }
</style>
