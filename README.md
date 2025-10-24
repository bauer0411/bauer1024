# bauer1024
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>빙고 게임</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
        }
        h1 {
            color: #333;
        }
        #bingo-card {
            display: grid;
            grid-template-columns: repeat(5, 60px);
            grid-gap: 5px;
            margin: 20px auto;
            justify-content: center;
        }
        .cell {
            width: 60px;
            height: 60px;
            background-color: #fff;
            border: 1px solid #ccc;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            user-select: none;
        }
        .cell.marked {
            background-color: #ffcc00;
        }
        .cell.free {
            background-color: #4caf50;
            color: white;
        }
        #draw-button {
            padding: 10px 20px;
            font-size: 16px;
            background-color: #2196f3;
            color: white;
            border: none;
            cursor: pointer;
            margin: 10px;
        }
        #drawn-numbers {
            margin: 10px;
            font-size: 18px;
            color: #e74c3c;
        }
        #status {
            font-size: 20px;
            color: #27ae60;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>빙고 게임</h1>
    <div id="bingo-card"></div>
    <button id="draw-button">숫자 추첨</button>
    <div id="drawn-numbers">추첨된 숫자: </div>
    <div id="status"></div>

    <script>
        const cardSize = 5;
        const maxNumber = 75;
        const bingoCard = document.getElementById('bingo-card');
        const drawButton = document.getElementById('draw-button');
        const drawnNumbersDiv = document.getElementById('drawn-numbers');
        const statusDiv = document.getElementById('status');

        let card = [];
        let drawnNumbers = new Set();
        let markedCells = new Set();

        // 빙고 카드 생성
        function generateCard() {
            let numbers = new Set();
            while (numbers.size < cardSize * cardSize - 1) {
                numbers.add(Math.floor(Math.random() * maxNumber) + 1);
            }
            numbers = Array.from(numbers);

            card = [];
            for (let i = 0; i < cardSize; i++) {
                let row = [];
                for (let j = 0; j < cardSize; j++) {
                    if (i === 2 && j === 2) {
                        row.push('FREE');
                    } else {
                        row.push(numbers.shift());
                    }
                }
                card.push(row);
            }
            renderCard();
        }

        // 카드 렌더링
        function renderCard() {
            bingoCard.innerHTML = '';
            for (let i = 0; i < cardSize; i++) {
                for (let j = 0; j < cardSize; j++) {
                    let cell = document.createElement('div');
                    cell.classList.add('cell');
                    cell.textContent = card[i][j];
                    cell.dataset.row = i;
                    cell.dataset.col = j;
                    if (card[i][j] === 'FREE') {
                        cell.classList.add('free', 'marked');
                        markedCells.add(`${i},${j}`);
                    }
                    cell.addEventListener('click', () => markCell(i, j));
                    bingoCard.appendChild(cell);
                }
            }
        }

        // 셀 마킹
        function markCell(row, col) {
            if (card[row][col] === 'FREE') return;
            let cell = bingoCard.querySelector(`[data-row="${row}"][data-col="${col}"]`);
            if (!cell.classList.contains('marked')) {
                cell.classList.add('marked');
                markedCells.add(`${row},${col}`);
                checkBingo();
            }
        }

        // 빙고 체크
        function checkBingo() {
            let bingo = false;

            // 가로 체크
            for (let i = 0; i < cardSize; i++) {
                if (Array.from({length: cardSize}, (_, j) => markedCells.has(`${i},${j}`)).every(Boolean)) bingo = true;
            }

            // 세로 체크
            for (let j = 0; j < cardSize; j++) {
                if (Array.from({length: cardSize}, (_, i) => markedCells.has(`${i},${j}`)).every(Boolean)) bingo = true;
            }

            // 대각선 체크
            if (Array.from({length: cardSize}, (_, i) => markedCells.has(`${i},${i}`)).every(Boolean)) bingo = true;
            if (Array.from({length: cardSize}, (_, i) => markedCells.has(`${i},${cardSize - 1 - i}`)).every(Boolean)) bingo = true;

            if (bingo) {
                statusDiv.textContent = '빙고! 축하합니다!';
                drawButton.disabled = true;
            }
        }

        // 숫자 추첨
        function drawNumber() {
            let num;
            do {
                num = Math.floor(Math.random() * maxNumber) + 1;
            } while (drawnNumbers.has(num));
            drawnNumbers.add(num);
            drawnNumbersDiv.textContent += ` ${num}`;
            
            // 자동으로 해당 숫자 마킹 (추첨된 숫자가 카드에 있으면)
            for (let i = 0; i < cardSize; i++) {
                for (let j = 0; j < cardSize; j++) {
                    if (card[i][j] === num) {
                        markCell(i, j);
                    }
                }
            }
        }

        // 초기화
        generateCard();
        drawButton.addEventListener('click', drawNumber);
    </script>
</body>
</html>
