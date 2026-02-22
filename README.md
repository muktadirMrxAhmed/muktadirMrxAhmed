<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nursing Knowledge Quiz</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>

<div class="quiz-container">
  <h1>Nursing Knowledge Quiz</h1>
  
  <div id="quiz-content">
    <div class="question" id="question">Question text goes here</div>
    <div class="btn-grid" id="answer-buttons">
      </div>
  </div>

  <div class="controls">
    <button id="next-btn">Next Question</button>
  </div>

  <div class="score-board" id="score-board">
    You scored <span id="score">0</span> out of <span id="total">0</span>!
    <br><br>
    <button class="btn restart-btn" onclick="startQuiz()">Restart Quiz</button>
  </div>
</div>

<script src="script.js"></script>
</body>
</html>
