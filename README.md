# google-mimicking-spreadsheets

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spreadsheet App</title>
    <style>
        body { font-family: Arial, sans-serif; }
        .spreadsheet {
            display: grid;
            grid-template-columns: repeat(6, 100px);
            gap: 1px;
            border: 1px solid black;
        }
        .cell {
            width: 100px;
            height: 30px;
            border: 1px solid #ccc;
            text-align: center;
            cursor: pointer;
            background: white;
        }
        .bold { font-weight: bold; }
        .italic { font-style: italic; }
    </style>
</head>
<body>
    <h2>Simple Spreadsheet</h2>
    <div class="spreadsheet" id="spreadsheet"></div>

    <h3>Operations</h3>
    <button onclick="calculate('sum')">Sum</button>
    <button onclick="calculate('average')">Average</button>
    <button onclick="calculate('min')">Min</button>
    <button onclick="calculate('max')">Max</button>
    <button onclick="calculate('count')">Count</button>
    <p id="result"></p>

    <h3>Editing</h3>
    <button onclick="format('bold')">Bold</button>
    <button onclick="format('italic')">Italic</button>
    <label for="fontSize">Font Size:</label>
    <input type="number" id="fontSize" min="8" max="36" value="12" onchange="format('fontSize')">
    <label for="fontColor">Font Color:</label>
    <input type="color" id="fontColor" onchange="format('color')">

    <h3>Table Management</h3>
    <button onclick="addRow()">Add Row</button>
    <button onclick="addColumn()">Add Column</button>

    <h3>Data Cleaning</h3>
    <button onclick="removeDuplicates()">Remove Duplicates</button>
    <button onclick="trimCells()">Trim</button>
    <button onclick="convertCase('upper')">Upper</button>
    <button onclick="convertCase('lower')">Lower</button>
    <button onclick="findAndReplace(prompt('Find:'), prompt('Replace with:'))">Find & Replace</button>

    <script>
        let rows = 10;
        let cols = 6;
        const spreadsheet = document.getElementById("spreadsheet");
        spreadsheet.style.gridTemplateColumns = `repeat(${cols}, 100px)`;

        let cells = [];

        function createCell(row, col) {
            const cell = document.createElement("div");
            cell.classList.add("cell");
            cell.contentEditable = true;
            cell.dataset.row = row;
            cell.dataset.col = col;

            spreadsheet.appendChild(cell);
            cells.push(cell);
        }

        function generateTable() {
            spreadsheet.innerHTML = "";
            cells = [];
            for (let r = 0; r < rows; r++) {
                for (let c = 0; c < cols; c++) {
                    createCell(r, c);
                }
            }
        }

        function calculate(operation) {
            let values = cells.map(cell => parseFloat(cell.innerText)).filter(num => !isNaN(num));
            if (values.length === 0) {
                document.getElementById("result").textContent = `No valid numbers found.`;
                return;
            }

            let result;
            switch (operation) {
                case 'sum': result = values.reduce((a, b) => a + b, 0); break;
                case 'average': result = values.reduce((a, b) => a + b, 0) / values.length; break;
                case 'min': result = Math.min(...values); break;
                case 'max': result = Math.max(...values); break;
                case 'count': result = values.length; break;
                default: result = "Invalid operation";
            }

            document.getElementById("result").textContent = `${operation.toUpperCase()}: ${result}`;
        }

        function format(type) {
            let selectedCell = document.activeElement;
            if (!selectedCell || !selectedCell.classList.contains("cell")) return;

            if (type === "bold") {
                selectedCell.classList.toggle("bold");
            } else if (type === "italic") {
                selectedCell.classList.toggle("italic");
            } else if (type === "fontSize") {
                selectedCell.style.fontSize = document.getElementById("fontSize").value + "px";
            } else if (type === "color") {
                selectedCell.style.color = document.getElementById("fontColor").value;
            }
        }

        function addRow() {
            rows++;
            generateTable();
        }

        function addColumn() {
            cols++;
            spreadsheet.style.gridTemplateColumns = `repeat(${cols}, 100px)`;
            generateTable();
        }

        function removeDuplicates() {
            let uniqueValues = new Set();
            let duplicatesFound = false;

            cells.forEach(cell => {
                let value = cell.innerText.trim();
                if (value && uniqueValues.has(value)) {
                    cell.innerText = "";
                    duplicatesFound = true;
                } else {
                    uniqueValues.add(value);
                }
            });

            alert(duplicatesFound ? "Duplicates removed successfully!" : "No duplicates found.");
        }

        function findAndReplace(findText, replaceText) {
            if (!findText) return;
            cells.forEach(cell => {
                if (cell.innerText.includes(findText)) {
                    cell.innerText = cell.innerText.replace(new RegExp(findText, 'g'), replaceText);
                }
            });
        }

        function trimCells() {
            let trimmed = false;
            cells.forEach(cell => {
                let originalText = cell.innerText;
                let trimmedText = originalText.replace(/^\s+|\s+$/g, ""); // Trim regular spaces
                trimmedText = trimmedText.replace(/\u00A0+/g, ""); // Remove non-breaking spaces

                if (originalText !== trimmedText) {
                    cell.innerText = trimmedText;
                    trimmed = true;
                }
            });
            alert(trimmed ? "All cells trimmed successfully!" : "No extra spaces found.");
        }

        function convertCase(type) {
            let modified = false;
            cells.forEach(cell => {
                let originalText = cell.innerText;
                if (type === "upper" && originalText !== originalText.toUpperCase()) {
                    cell.innerText = originalText.toUpperCase();
                    modified = true;
                } else if (type === "lower" && originalText !== originalText.toLowerCase()) {
                    cell.innerText = originalText.toLowerCase();
                    modified = true;
                }
            });
            alert(modified ? `Converted to ${type.toUpperCase()} successfully!` : `No changes needed.`);
        }

        generateTable();
    </script>
</body>
</html>
