<template>
    <div id="app" class="blue-background">
      <!-- Kalkulator Content -->
      <h1 style="color: black;">Kalkulator {{ currentMode }}</h1>
      <div class="mode-selector">
        <button @click="changeMode('Sederhana')" :class="{ 'active': currentMode === 'Sederhana' }">Sederhana</button>
        <button @click="changeMode('Ilmiah')" :class="{ 'active': currentMode === 'Ilmiah' }">Ilmiah</button>
      </div>
      <div class="style-selector">
        <label for="styleSelect" style="color: black;">Pilih gaya:</label>
        <select id="styleSelect" v-model="selectedStyle">
          <option v-for="(style, index) in styles" :key="index" :value="style.class">{{ style.name }}</option>
        </select>
      </div>
      <table class="calculator" :class="[selectedStyle]">
        <!-- Expression Input -->
        <tr>
          <td colspan="4">
            <div class="expression-input">
              <label for="expressionInput" style="color: black;">Ekspresi Matematika:</label>
              <input type="text" id="expressionInput" v-model="expression" @keydown.enter="evaluate" placeholder="Masukkan ekspresi matematika">
              <input type="text" v-model="result" disabled>
            </div>
          </td>
        </tr>
        <!-- Buttons -->
        <tr class="buttons-row" v-for="(row, rowIndex) in modeButtonsChunks" :key="rowIndex">
          <td v-for="(button, index) in row" :key="index">
            <button :class="{ 'operator': button.type === 'operator', 'function': button.type === 'function', 'number': button.type === 'number' }" 
                    :style="{ backgroundColor: button.backgroundColor, color: button.color }" 
                    @click="handleButtonClick(button.label)">
              {{ button.label }}
            </button>
          </td>
        </tr>
      </table>
      <!-- Tanda Pembuat -->
      <p style="color: black; font-size: 12px; position: fixed; bottom: 5px; left: 5px;">cr.ervilidyawati</p>
    </div>
  </template>
  
  <script>
  export default {
    name: 'App',
    data() {
      return {
        expression: '',
        result: '',
        currentMode: 'Sederhana', // Default mode
        selectedStyle: 'default-style', // Default style
        styles: [
          { name: 'Default', class: 'default-style' },
          { name: 'Classic', class: 'classic-style' },
          { name: 'Modern', class: 'modern-style' },
          { name: 'Colorful', class: 'colorful-style' }
        ],
        calculatorModes: {
          'Sederhana': [
            { label: '1', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '2', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '3', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '+', type: 'operator', backgroundColor: '#f09c23', color: '#ffffff' },
            { label: '4', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '5', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '6', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '-', type: 'operator', backgroundColor: '#f09c23', color: '#ffffff' },
            { label: '7', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '8', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '9', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '*', type: 'operator', backgroundColor: '#f09c23', color: '#ffffff' },
            { label: '0', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '.', type: 'number', backgroundColor: '#f0f0f0', color: '#333333' },
            { label: '/', type: 'operator', backgroundColor: '#f09c23', color: '#ffffff' },
            { label: 'C', type: 'function', backgroundColor: '#afafaf', color: '#ffffff' },
            { label: '=', type: 'function', backgroundColor: '#53a552', color: '#ffffff' }
          ],
          'Ilmiah': [
            { label: 'sin', type: 'function', backgroundColor: '#6c7bd8', color: '#ffffff' },
            { label: 'cos', type: 'function', backgroundColor: '#6c7bd8', color: '#ffffff' },
            { label: 'tan', type: 'function', backgroundColor: '#6c7bd8', color: '#ffffff' },
            { label: 'log', type: 'function', backgroundColor: '#6c7bd8', color: '#ffffff' },
            { label: '%', type: 'operator', backgroundColor: '#f09c23', color: '#ffffff' }, // Changed from 'sqrt' to '%'
            { label: '^', type: 'operator', backgroundColor: '#f09c23', color: '#ffffff' }, // Added '^' for exponentiation
            { label: 'C', type: 'function', backgroundColor: '#afafaf', color: '#ffffff' },
            { label: '=', type: 'function', backgroundColor: '#53a552', color: '#ffffff' }
          ]
        }
      }
    },
    computed: {
      modeButtons() {
        return this.calculatorModes[this.currentMode];
      },
      modeButtonsChunks() {
        // Split buttons into chunks of 4 for each row
        const chunkSize = 4;
        const chunks = [];
        let i, j;
        for (i = 0, j = this.modeButtons.length; i < j; i += chunkSize) {
          chunks.push(this.modeButtons.slice(i, i + chunkSize));
        }
        return chunks;
      }
    },
    methods: {
      appendToExpression(value) {
        if (value === 'C') {
          this.expression = '';
        } else if (value === '=') {
          this.evaluate();
        } else if (value === '(' || value === ')') {
          this.expression += value;
        } else if (value === 'sin' || value === 'cos' || value === 'tan' || value === 'log') {
          this.expression += value + '(';
          // Add closing parenthesis after function name
          this.expression += ')';
        } else {
          this.expression += value;
        }
      },
      evaluate() {
        try {
          if (this.currentMode === 'Sederhana') {
            this.result = eval(this.expression);
          } else if (this.currentMode === 'Ilmiah') {
            let result;
            // Check if the expression contains trigonometric functions, logarithm, or square root
            if (this.expression.startsWith('sin(') || this.expression.startsWith('cos(') || this.expression.startsWith('tan(')) {
              // Separate function name and argument
              const functionName = this.expression.slice(0, 3);
              const argument = parseFloat(this.expression.slice(4, -1));
              // Convert angle from degree to radian before evaluating
              const angleInRadians = argument * (Math.PI / 180);
              result = Math[functionName](angleInRadians);
            } else if (this.expression === 'log') {
              if (this.result !== undefined && this.result > 0 && !isNaN(this.result)) {
                result = Math.log(this.result);
              } else {
                throw "Invalid logarithm operand";
              }
            } else if (this.expression === '%') { // Check for modulus operator
              // Split the expression by modulus operator and evaluate the result
              const parts = this.expression.split('%');
              if (parts.length === 2) {
                const dividend = parseFloat(parts[0]);
                const divisor = parseFloat(parts[1]);
                if (!isNaN(dividend) && !isNaN(divisor) && divisor !== 0) {
                  result = dividend % divisor;
                } else {
                  throw "Invalid modulus operands";
                }
              } else {
                throw "Invalid modulus expression";
              }
            } else if (this.expression.includes('^')) { // Check for exponentiation operator
              const parts = this.expression.split('^');
              if (parts.length === 2) {
                const base = parseFloat(parts[0]);
                const exponent = parseFloat(parts[1]);
                result = Math.pow(base, exponent);
              } else {
                throw "Invalid exponentiation expression";
              }
            } else {
              result = eval(this.expression);
              if (isNaN(result)) {
                throw "Invalid expression";
              }
            }
            this.result = result;
          }
        } catch (error) {
          this.result = 'Error: ' + error;
        }
      },
      handleButtonClick(value) {
        this.appendToExpression(value);
      },
      changeMode(mode) {
        this.currentMode = mode;
      }
    }
  }
  </script>
  
  <style scoped>
  #app {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    text-align: center;
    
  }
  
  .mode-selector {
    margin-bottom: 10px;
  }
  
  .mode-selector button {
    margin: 5px;
    padding: 5px 10px;
    font-size: 16px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .mode-selector button.active {
    background-color: #cb82f0;
    color: #ffffff;
  }
  
  .style-selector {
    margin-bottom: 10px;
  }
  
  .calculator {
    margin: 20px auto;
    padding: 10px;
    border-radius: 15px; /* Melengkungkan sudut tabel */
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    background-color: #e1f1ff;
  }
  
  .expression-input {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .expression-input label {
    margin-bottom: 5px;
  }
  
  .calculator input {
    width: calc(100% - 20px);
    padding: 10px;
    margin-bottom: 10px;
    border: none;
    border-radius: 5px;
  }
  
  .buttons-row {
    height: 50px;
  }
  
  .calculator button {
    width: calc(100% - 10px);
    height: 100%;
    font-size: 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    color: black; /* Mengubah warna teks menjadi hitam */
  }
  
  .operator {
    color: #ffffff;
  }
  
  .function {
    color: #ffffff;
  }
  
  .number {
    color: #333333;
  }
  
  .result {
    font-weight: bold;
    margin-top: 10px;
  }
  
  /* Custom Styles */
  .default-style {
    background-color: #ddd;
  }
  
  .modern-style {
    background-color: #f2f2f2;
  }
  
  .classic-style {
    background-color: #ccc;
  }
  
  .classic-style .calculator button {
    font-size: 24px;
  }
  
  .classic-style .calculator button.operator,
  .classic-style .calculator button.function {
    font-size: 24px;
    width: calc(50% - 5px);
  }
  
  .classic-style .calculator button.number {
    font-size: 24px;
    width: calc(25% - 5px);
  }
  
  .classic-style .calculator button.operator,
  .classic-style .calculator button.function,
  .classic-style .calculator button.number {
    height: 70px;
  }
  
  .colorful-style {
    background-color: #febac5;
  }
  
  .colorful-style .mode-selector button {
    background-color: #6FC4F5;
  }
  
  .colorful-style .calculator input {
    background-color: #FFE8C5;
  }
  
  .colorful-style .calculator button {
    background-color: #ab84f9;
    color: #ffffff;
  }
  
  .colorful-style .calculator button.operator {
    background-color: #9bcef1;
  }
  
  .colorful-style .calculator button.function {
    background-color: #6CB3FF;
  }
  
  .colorful-style .calculator button.number {
    background-color: #e5a5ff;
  }
  
  .colorful-style .result {
    color: #d0aefb;
  }
  
  .blue-background {
    background-color: #e1f1ff; /* Latar belakang biru muda untuk seluruh konten */
  }
  </style>
  