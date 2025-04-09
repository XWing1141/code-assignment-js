const PLAYER_X = "X";
const PLAYER_O = "O";
const board = document.getElementById("board");
let playerName = PLAYER_X;
let isGameActive = true; // Flag to track if the game is active

function makeColumnHtml(id, marker) {
    return `<td id="${id}" onclick="cellClick(this)" data-taken="false">[${marker}]</td>`;
}

function cellClick(cell) {
    // Prevent clicking on a square if the game is over or the square is already taken
    if (!isGameActive || cell.getAttribute("data-taken") === "true") {
        return;
    }

    // Mark the cell and set it as taken
    cell.innerHTML = `[${playerName}]`;
    cell.setAttribute("data-taken", "true");
    cell.setAttribute("data-player", playerName);

    // Check for a winner
    if (checkWinner()) {
        alert(`Player ${playerName} wins!`);
        isGameActive = false; // Disable further changes to the board
        return;
    }

    // Check for a draw
    if (isBoardFull()) {
        alert("It's a draw!");
        isGameActive = false; // Disable further changes to the board
        return;
    }

    // Switch the player
    switchPlayer();
}

function displayPlayerName() {
    document.getElementById("currentPlayer").innerText = "Current Player: " + playerName;
}

function switchPlayer() {
    playerName = (playerName == PLAYER_X) ? PLAYER_O : PLAYER_X;
    displayPlayerName();
}

function init() {
    playerName = PLAYER_X;
    isGameActive = true; // Reset the game state
    displayPlayerName();
    let boardHtml = "";

    for (let ix = 0; ix < 3; ix++) {
        boardHtml += "<tr>";

        for (let jx = 0; jx < 3; jx++) {
            boardHtml += makeColumnHtml(`${ix}_${jx}`, "_");
        }

        boardHtml += "</tr>";
    }

    board.innerHTML = boardHtml;

    // Reset all cells' data attributes
    const cells = board.getElementsByTagName("td");
    for (let cell of cells) {
        cell.setAttribute("data-taken", "false");
        cell.removeAttribute("data-player");
    }
}

function checkWinner() {
    const cells = board.getElementsByTagName("td");
    const grid = Array.from(cells).map(cell => cell.getAttribute("data-player") || "");

    // Check rows, columns, and diagonals
    for (let i = 0; i < 3; i++) {
        // Check rows
        if (grid[i * 3] && grid[i * 3] === grid[i * 3 + 1] && grid[i * 3] === grid[i * 3 + 2]) {
            return true;
        }
        // Check columns
        if (grid[i] && grid[i] === grid[i + 3] && grid[i] === grid[i + 6]) {
            return true;
        }
    }

    // Check diagonals
    if (grid[0] && grid[0] === grid[4] && grid[0] === grid[8]) {
        return true;
    }
    if (grid[2] && grid[2] === grid[4] && grid[2] === grid[6]) {
        return true;
    }

    return false;
}

function isBoardFull() {
    const cells = board.getElementsByTagName("td");
    return Array.from(cells).every(cell => cell.getAttribute("data-taken") === "true");
}

init();
