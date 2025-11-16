<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jeu de Mémoire - Mécanique Auto</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1a2a3a, #2c3e50);
            color: #fff;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            width: 100%;
            text-align: center;
        }

        header {
            margin-bottom: 30px;
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            color: #e74c3c;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }

        h1 i {
            margin-right: 15px;
            color: #3498db;
        }

        .subtitle {
            font-size: 1.2rem;
            color: #ecf0f1;
            margin-bottom: 20px;
        }

        .game-info {
            display: flex;
            justify-content: space-around;
            background: rgba(44, 62, 80, 0.7);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .info-box {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .info-label {
            font-size: 0.9rem;
            color: #bdc3c7;
            margin-bottom: 5px;
        }

        .info-value {
            font-size: 1.8rem;
            font-weight: bold;
            color: #e74c3c;
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
            margin-bottom: 30px;
            perspective: 1000px;
        }

        .card {
            height: 120px;
            background: #3498db;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.5rem;
            cursor: pointer;
            transform-style: preserve-3d;
            transition: transform 0.5s;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }

        .card.flipped {
            transform: rotateY(180deg);
        }

        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .card-front {
            background: #2c3e50;
            color: #ecf0f1;
        }

        .card-back {
            background: #ecf0f1;
            color: #2c3e50;
            transform: rotateY(180deg);
        }

        .controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-bottom: 20px;
        }

        button {
            padding: 12px 25px;
            font-size: 1.1rem;
            background: #e74c3c;
            color: white;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        button:hover {
            background: #c0392b;
            transform: translateY(-3px);
            box-shadow: 0 6px 8px rgba(0, 0, 0, 0.15);
        }

        button:active {
            transform: translateY(-1px);
        }

        .message {
            font-size: 1.3rem;
            margin-top: 20px;
            padding: 15px;
            background: rgba(46, 204, 113, 0.2);
            border-radius: 10px;
            display: none;
        }

        .instructions {
            background: rgba(0, 0, 0, 0.3);
            padding: 20px;
            border-radius: 10px;
            margin-top: 20px;
            text-align: left;
        }

        .instructions h2 {
            color: #3498db;
            margin-bottom: 10px;
            text-align: center;
        }

        .instructions ul {
            list-style-type: none;
            padding-left: 20px;
        }

        .instructions li {
            margin-bottom: 10px;
            display: flex;
            align-items: center;
        }

        .instructions i {
            color: #e74c3c;
            margin-right: 10px;
        }

        /* Animation pour les paires trouvées */
        @keyframes matchAnimation {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }

        .matched {
            animation: matchAnimation 0.5s ease;
        }

        /* Responsive */
        @media (max-width: 600px) {
            .game-board {
                grid-template-columns: repeat(3, 1fr);
            }
            
            h1 {
                font-size: 2rem;
            }
            
            .card {
                height: 100px;
                font-size: 2rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-car"></i>Jeu de Mémoire Mécanique</h1>
            <p class="subtitle">Retrouvez les paires de pièces automobiles identiques !</p>
        </header>

        <div class="game-info">
            <div class="info-box">
                <div class="info-label">COUPS</div>
                <div class="info-value" id="moves">0</div>
            </div>
            <div class="info-box">
                <div class="info-label">TEMPS</div>
                <div class="info-value" id="timer">0s</div>
            </div>
            <div class="info-box">
                <div class="info-label">SCORE</div>
                <div class="info-value" id="score">0</div>
            </div>
        </div>

        <div class="game-board" id="gameBoard">
            <!-- Les cartes seront générées par JavaScript -->
        </div>

        <div class="controls">
            <button id="startBtn"><i class="fas fa-play"></i> Nouvelle Partie</button>
            <button id="resetBtn"><i class="fas fa-redo"></i> Recommencer</button>
        </div>

        <div class="message" id="message">
            Félicitations ! Vous avez terminé le jeu !
        </div>

        <div class="instructions">
            <h2><i class="fas fa-info-circle"></i> Comment jouer</h2>
            <ul>
                <li><i class="fas fa-mouse-pointer"></i> Cliquez sur deux cartes pour les retourner</li>
                <li><i class="fas fa-puzzle-piece"></i> Trouvez toutes les paires de pièces automobiles identiques</li>
                <li><i class="fas fa-tachometer-alt"></i> Terminez le jeu avec le moins de coups possible</li>
                <li><i class="fas fa-stopwatch"></i> Soyez rapide pour obtenir un meilleur score</li>
            </ul>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // Éléments du DOM
            const gameBoard = document.getElementById('gameBoard');
            const movesDisplay = document.getElementById('moves');
            const timerDisplay = document.getElementById('timer');
            const scoreDisplay = document.getElementById('score');
            const startBtn = document.getElementById('startBtn');
            const resetBtn = document.getElementById('resetBtn');
            const message = document.getElementById('message');

            // Variables du jeu
            let cards = [];
            let flippedCards = [];
            let moves = 0;
            let matches = 0;
            let score = 0;
            let timer = 0;
            let timerInterval;
            let gameStarted = false;

            // Pièces automobiles avec leurs icônes FontAwesome
            const carParts = [
                { name: 'moteur', icon: 'fas fa-cogs' },
                { name: 'volant', icon: 'fas fa-car' },
                { name: 'pneu', icon: 'fas fa-tire' },
                { name: 'batterie', icon: 'fas fa-car-battery' },
                { name: 'feu', icon: 'fas fa-lightbulb' },
                { name: 'clé', icon: 'fas fa-key' },
                { name: 'outil', icon: 'fas fa-tools' },
                { name: 'jauge', icon: 'fas fa-tachometer-alt' }
            ];

            // Initialisation du jeu
            function initGame() {
                // Réinitialiser les variables
                cards = [];
                flippedCards = [];
                moves = 0;
                matches = 0;
                score = 0;
                timer = 0;
                gameStarted = false;
                
                // Mettre à jour l'affichage
                movesDisplay.textContent = moves;
                scoreDisplay.textContent = score;
                timerDisplay.textContent = '0s';
                message.style.display = 'none';
                
                // Arrêter le timer s'il est en cours
                clearInterval(timerInterval);
                
                // Créer les cartes
                createCards();
                
                // Mélanger les cartes
                shuffleCards();
                
                // Afficher les cartes
                renderCards();
            }

            // Créer les cartes (doubler chaque pièce pour former des paires)
            function createCards() {
                cards = [];
                carParts.forEach(part => {
                    // Ajouter deux cartes pour chaque pièce
                    cards.push({ ...part, id: part.name + '-1' });
                    cards.push({ ...part, id: part.name + '-2' });
                });
            }

            // Mélanger les cartes
            function shuffleCards() {
                for (let i = cards.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [cards[i], cards[j]] = [cards[j], cards[i]];
                }
            }

            // Afficher les cartes sur le plateau
            function renderCards() {
                gameBoard.innerHTML = '';
                cards.forEach(card => {
                    const cardElement = document.createElement('div');
                    cardElement.className = 'card';
                    cardElement.dataset.id = card.id;
                    
                    const cardFront = document.createElement('div');
                    cardFront.className = 'card-front';
                    cardFront.innerHTML = '<i class="fas fa-question"></i>';
                    
                    const cardBack = document.createElement('div');
                    cardBack.className = 'card-back';
                    cardBack.innerHTML = `<i class="${card.icon}"></i>`;
                    
                    cardElement.appendChild(cardFront);
                    cardElement.appendChild(cardBack);
                    
                    cardElement.addEventListener('click', () => flipCard(cardElement));
                    gameBoard.appendChild(cardElement);
                });
            }

            // Retourner une carte
            function flipCard(card) {
                // Ne pas retourner la carte si elle est déjà retournée ou si le jeu n'a pas commencé
                if (card.classList.contains('flipped') || flippedCards.length >= 2 || !gameStarted) {
                    return;
                }
                
                // Retourner la carte
                card.classList.add('flipped');
                flippedCards.push(card);
                
                // Vérifier si deux cartes sont retournées
                if (flippedCards.length === 2) {
                    // Incrémenter le compteur de coups
                    moves++;
                    movesDisplay.textContent = moves;
                    
                    // Vérifier si les cartes correspondent
                    checkForMatch();
                }
            }

            // Vérifier si les deux cartes retournées correspondent
            function checkForMatch() {
                const card1 = flippedCards[0];
                const card2 = flippedCards[1];
                
                // Obtenir les noms des pièces à partir des IDs des cartes
                const part1 = card1.dataset.id.split('-')[0];
                const part2 = card2.dataset.id.split('-')[0];
                
                if (part1 === part2) {
                    // Les cartes correspondent
                    card1.classList.add('matched');
                    card2.classList.add('matched');
                    
                    // Augmenter le compteur de paires trouvées
                    matches++;
                    
                    // Calculer le score
                    score += 100 - (moves * 2);
                    if (score < 0) score = 0;
                    scoreDisplay.textContent = score;
                    
                    // Vider le tableau des cartes retournées
                    flippedCards = [];
                    
                    // Vérifier si le jeu est terminé
                    if (matches === carParts.length) {
                        endGame();
                    }
                } else {
                    // Les cartes ne correspondent pas
                    setTimeout(() => {
                        card1.classList.remove('flipped');
                        card2.classList.remove('flipped');
                        flippedCards = [];
                    }, 1000);
                }
            }

            // Démarrer le jeu
            function startGame() {
                if (!gameStarted) {
                    gameStarted = true;
                    
                    // Démarrer le timer
                    timerInterval = setInterval(() => {
                        timer++;
                        timerDisplay.textContent = `${timer}s`;
                    }, 1000);
                }
            }

            // Terminer le jeu
            function endGame() {
                clearInterval(timerInterval);
                message.style.display = 'block';
                message.innerHTML = `
                    <i class="fas fa-trophy"></i> Félicitations ! 
                    Vous avez terminé le jeu en ${moves} coups et ${timer} secondes.
                    Votre score final est ${score} points !
                `;
                gameStarted = false;
            }

            // Événements des boutons
            startBtn.addEventListener('click', () => {
                initGame();
                startGame();
            });

            resetBtn.addEventListener('click', initGame);

            // Initialiser le jeu au chargement de la page
            initGame();
        });
    </script>
</body>
</html>
