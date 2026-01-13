<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Moves Predictor</title>
    <link rel="stylesheet" href="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.css">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://unpkg.com/chess.js@0.12.0/chess.js"></script>
    <script src="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }
        #board {
            width: 400px;
        }
        .controls {
            margin-top: 20px;
        }
        input, button {
            margin: 5px;
            padding: 10px;
        }
    </style>
</head>
<body>
    <h1>Chess Moves Predictor</h1>
    <div id="board"></div>
    <div class="controls">
        <input type="text" id="fenInput" placeholder="Enter FEN (e.g., rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1)" value="rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1">
        <button id="loadBtn">Load Position</button>
        <button id="predictBtn">Predict Move</button>
        <p id="prediction">Prediction: None yet</p>
    </div>

    <script>
        var board = null;
        var game = new Chess();

        function onDragStart(source, piece, position, orientation) {
            // Allow dragging only if it's the player's turn (for demo, allow all)
            return true;
        }

        function onDrop(source, target) {
            // Update the game state
            var move = game.move({
                from: source,
                to: target,
                promotion: 'q' // Always promote to queen for simplicity
            });

            if (move === null) return 'snapback';

            updateBoard();
        }

        function updateBoard() {
            board.position(game.fen());
        }

        var config = {
            draggable: true,
            position: 'start',
            onDragStart: onDragStart,
            onDrop: onDrop,
            onSnapEnd: updateBoard
        };
        board = Chessboard('board', config);

        $('#loadBtn').on('click', function() {
            var fen = $('#fenInput').val();
            if (game.load(fen)) {
                board.position(fen);
                $('#prediction').text('Prediction: None yet');
            } else {
                alert('Invalid FEN');
            }
        });

        $('#predictBtn').on('click', function() {
            var moves = game.moves();
            if (moves.length === 0) {
                $('#prediction').text('Prediction: Game over');
                return;
            }
            // Simple prediction: Pick a random legal move
            var randomMove = moves[Math.floor(Math.random() * moves.length)];
            game.move(randomMove);
            board.position(game.fen());
            $('#prediction').text('Prediction: ' + randomMove);
        });
    </script>
</body>
</html>
