<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>GRAVIBUS : Sauve le Bus !</title>
  <style>
    body {
      background: linear-gradient(to bottom, #0d0d2b, #1a1a40);
      color: #000;
      font-family: 'Comic Sans MS', cursive;
      text-align: center;
      padding: 2em;
      background-image: url('https://www.transparenttextures.com/patterns/stardust.png');
    }
    h1 {
      font-size: 2.5em;
      margin-bottom: 0.2em;
    }
    .question-box {
      background: rgba(255, 255, 255, 0.1);
      border-radius: 20px;
      padding: 1.5em;
      margin: 1em auto;
      max-width: 600px;
      box-shadow: 0 0 15px #0ff;
    }
    button {
      background-color: #00bcd4;
      color: white;
      border: none;
      padding: 1em;
      margin: 0.5em;
      border-radius: 10px;
      font-size: 1em;
      cursor: pointer;
    }
    button:hover {
      background-color: #0097a7;
    }
    #result {
      font-size: 1.4em;
      margin-top: 2em;
    }
    .team-score {
      font-size: 1.2em;
      margin-top: 1em;
    }
    #mode-select {
      margin-top: 2em;
    }
    #name-input {
      font-size: 1.2em;
      padding: 0.5em;
      margin-top: 1em;
      border-radius: 5px;
    }
    #start-btn {
      background-color: #00bcd4;
      color: white;
      padding: 1em;
      border-radius: 10px;
      font-size: 1.2em;
      cursor: pointer;
    }
    #ranking {
      margin-top: 2em;
      font-size: 1.4em;
    }
  </style>
</head>
<body>
  <h1>🚀 GRAVIBUS : Sauve le Bus ! 🚍</h1>

  <p style="max-width: 700px; margin: 0 auto; font-size: 1.2em; background-color: rgba(255,255,255,0.2); padding: 1em; border-radius: 10px;">
    🔧 Le bus scolaire est en danger ! Seules tes connaissances sur la gravité et les forces peuvent le sauver. 🧠  
    Réponds aux questions et mène ta mission à bien. Prêt(e) pour l’aventure ?
  </p>

  <!-- Formulaire pour entrer le nom -->
  <div id="mode-select">
    <label for="name-input">Entrez votre nom :</label>
    <input type="text" id="name-input" placeholder="Votre nom ici" />
    <button id="start-btn" onclick="startGame()">Commencer</button>
  </div>

  <div class="question-box" id="quiz-box" style="display:none">
    <p id="question"></p>
    <div id="choices"></div>
  </div>
  <div id="result"></div>

  <div id="ranking"></div>

  <div style="margin-top: 3em;">
    <h2>📱 Accède au jeu sur ton téléphone :</h2>
    <img src="https://api.qrserver.com/v1/create-qr-code/?data=https://ramhass.github.io/gravibus/&size=200x200" alt="QR code vers Gravibus">
    <p>Scanne le QR code ou visite :<br><a href="https://ramhass.github.io/gravibus/" style="color: #00bcd4;">https://ramhass.github.io/gravibus/</a></p>
  </div>

  <script>
    const questions = [
      { q: "Quelle force attire les objets vers la Terre ?", a: ["Électricité", "Gravité", "Magnétisme"], correct: 1 },
      { q: "Laquelle n'est PAS une unité de vitesse ?", a: ["km/h", "m/s", "kg/m"], correct: 2 },
      { q: "Si tu lances un ballon en l'air, que se passe-t-il ?", a: ["Il s'arrête en l'air", "Il continue à monter", "Il redescend"], correct: 2 },
      { q: "Plus un objet est lourd...", a: ["...moins la gravité agit sur lui", "...plus il va vite", "...plus la gravité agit sur lui"], correct: 2 },
      { q: "Quelle est la vitesse moyenne d'un bus ?", a: ["90 km/h", "300 km/h", "10 km/h"], correct: 0 },
      { q: "Sur la Lune, on pèse...", a: ["plus", "moins", "la même chose"], correct: 1 },
      { q: "Qu'est-ce qui ralentit un objet qui glisse ?", a: ["Le magnétisme", "Les frottements", "La lumière"], correct: 1 },
      { q: "La masse change-t-elle selon la planète ?", a: ["Oui", "Non", "Seulement le dimanche"], correct: 1 },
      { q: "Quel est l'effet d'une force sur un objet ?", a: ["Il explose", "Il change de mouvement", "Il devient invisible"], correct: 1 },
      { q: "Quand un bus freine brusquement...", a: ["On tombe en arrière", "On est projeté en avant", "On flotte"], correct: 1 },
      { q: "Que se passe-t-il dans l'espace si on pousse un objet ?", a: ["Il flotte sans s'arrêter", "Il tombe", "Il fond"], correct: 0 },
      { q: "Quel est le contraire d'une accélération ?", a: ["Décélération", "Gravitation", "Motivation"], correct: 0 },
      { q: "Pourquoi les astronautes flottent dans l'ISS ?", a: ["Pas de gravité locale", "L’ISS tombe en continu", "Ils sautent très haut"], correct: 1 },
      { q: "Quel est l'effet de la gravité sur la Lune ?", a: ["6x plus faible qu’ici", "Identique à la Terre", "N’existe pas"], correct: 0 },
      { q: "Quand deux forces opposées s’annulent ?", a: ["L’objet s’arrête ou garde son mouvement", "Il explose", "Il va en rond"], correct: 0 }
    ];

    let current = 0;
    let score = 0;
    let playerName = "";
    let participants = [];

    function startGame() {
      playerName = document.getElementById("name-input").value;
      if (!playerName) {
        alert("Veuillez entrer un nom !");
        return;
      }

      document.getElementById("mode-select").style.display = "none";
      document.getElementById("quiz-box").style.display = "block";
      showQuestion();
    }

    function showQuestion() {
      document.getElementById("result").textContent = "";
      const q = questions[current];
      document.getElementById("question").textContent = `Question ${current + 1} : ${q.q}`;
      const choicesDiv = document.getElementById("choices");
      choicesDiv.innerHTML = "";

      q.a.forEach((choice, index) => {
        const btn = document.createElement("button");
        btn.textContent = choice;
        btn.onclick = () => selectAnswer(index);
        choicesDiv.appendChild(btn);
      });
    }

    function selectAnswer(index) {
      const q = questions[current];
      const isCorrect = index === q.correct;
      const sound = new Audio(isCorrect ? 'https://freesound.org/data/previews/256/256113_3263906-lq.mp3' : 'https://freesound.org/data/previews/250/250629_4486188-lq.mp3');
      sound.play();

      if (isCorrect) {
        score++;
      }
      document.getElementById("result").textContent = isCorrect ? "✅ Bonne réponse !" : "❌ Mauvaise réponse !";

      current++;
      setTimeout(() => {
        if (current < questions.length) {
          showQuestion();
        } else {
          document.getElementById("quiz-box").style.display = "none";
          document.getElementById("result").textContent = `🎉 Fin de mission, ${playerName} ! Vous avez sauvé ${score}/15 parties du bus !`;

          // Ajouter participant au classement
          participants.push({ name: playerName, score: score });

          // Trier les participants par score
          participants.sort((a, b) => b.score - a.score);

          // Afficher le classement
          let rankingText = "🏆 Classement :<br>";
          participants.forEach((participant, index) => {
            rankingText += `${index + 1}. ${participant.name} - ${participant.score} points<br>`;
          });
          document.getElementById("ranking").innerHTML = rankingText;
        }
      }, 1200);
    }
  </script>
</body>
</html>
