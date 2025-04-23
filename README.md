# Phrasal-verbs-game
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quiz Phrasal Verbs</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f0f2f5;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }
        h1 {
            color: #2c3e50;
            margin-bottom: 30px;
        }
        .quiz-container {
            background: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 500px;
        }
        .question {
            font-size: 1.3em;
            margin-bottom: 20px;
            color: #2c3e50;
        }
        .options {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-bottom: 20px;
        }
        .option {
            padding: 12px;
            background: #e9ecef;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
        }
        .option:hover {
            background: #dee2e6;
        }
        .option.selected {
            background: #4dabf7;
            color: white;
        }
        .option.correct {
            background: #40c057;
            color: white;
        }
        .option.incorrect {
            background: #fa5252;
            color: white;
        }
        button {
            padding: 12px 20px;
            background: #4dabf7;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1em;
        }
        button:disabled {
            background: #adb5bd;
            cursor: not-allowed;
        }
        .score {
            margin-top: 20px;
            font-size: 1.2em;
            font-weight: bold;
        }
        .progress {
            margin-top: 10px;
            color: #868e96;
        }
    </style>
</head>
<body>
    <h1>Quiz Phrasal Verbs</h1>
    
    <div class="quiz-container">
        <div class="question" id="question">¿Qué significa "<span id="verb">go away</span>"?</div>
        <div class="options" id="options">
            <div class="option" onclick="selectOption(this, 'A')">A) irse / desaparecer (problema, dolor)</div>
            <div class="option" onclick="selectOption(this, 'B')">B) tocar / presionar</div>
            <div class="option" onclick="selectOption(this, 'C')">C) construir / edificar</div>
        </div>
        <button id="next-btn" onclick="nextQuestion()" disabled>Siguiente</button>
    </div>
    
    <div class="score">Puntaje: <span id="score">0</span>/20</div>
    <div class="progress">Pregunta <span id="current">1</span> de 20</div>

    <script>
        // Base de datos completa con respuestas correctas e incorrectas
        const questions = [
            {
                verb: "add to",
                correct: "añadir / aumentar",
                incorrect: ["borrar / eliminar", "dibujar / pintar"]
            },
            {
                verb: "cut down",
                correct: "reducir / talar",
                incorrect: ["construir / edificar", "celebrar / festejar"]
            },
            {
                verb: "dress up",
                correct: "vestirse elegante / disfrazarse",
                incorrect: ["desvestirse / desnudarse", "correr / saltar"]
            },
            {
                verb: "fit in with",
                correct: "encajar con / coincidir con",
                incorrect: ["pelear / discutir", "ignorar / evitar"]
            },
            {
                verb: "go out",
                correct: "salir / apagarse (luz o fuego)",
                incorrect: ["entrar / encender", "dormir / descansar"]
            },
            {
                verb: "keep up with",
                correct: "mantenerse al día con / seguir el ritmo de",
                incorrect: ["olvidar / ignorar", "correr / saltar"]
            },
            {
                verb: "pull on",
                correct: "ponerse (ropa rápidamente) / tirar de algo",
                incorrect: ["quitarse / soltar", "empujar / rechazar"]
            },
            {
                verb: "put together",
                correct: "armar / juntar",
                incorrect: ["desarmar / separar", "romper / destruir"]
            },
            {
                verb: "save up",
                correct: "ahorrar / guardar dinero para algo",
                incorrect: ["gastar / derrochar", "pedir / solicitar"]
            },
            {
                verb: "slip on",
                correct: "ponerse (ropa fácilmente) / resbalar",
                incorrect: ["quitarse / agarrar", "correr / saltar"]
            },
            {
                verb: "stand out",
                correct: "destacar / resaltar",
                incorrect: ["pasar desapercibido / esconderse", "caer / tropezar"]
            },
            {
                verb: "take back",
                correct: "devolver / retractarse",
                incorrect: ["tomar / aceptar", "guardar / conservar"]
            },
            {
                verb: "go on",
                correct: "continuar / suceder",
                incorrect: ["detenerse / parar", "empezar / comenzar"]
            },
            {
                verb: "go over",
                correct: "repasar / cruzar",
                incorrect: ["ignorar / evitar", "olvidar / descuidar"]
            },
            {
                verb: "go back",
                correct: "regresar / remontarse (en el tiempo)",
                incorrect: ["avanzar / progresar", "esperar / permanecer"]
            },
            {
                verb: "go away",
                correct: "irse / desaparecer (problema, dolor)",
                incorrect: ["tocar / presionar", "construir / edificar"]
            },
            {
                verb: "go off",
                correct: "sonar (alarma) / explotar",
                incorrect: ["callar / silenciar", "construir / armar"]
            },
            {
                verb: "go in",
                correct: "entrar / participar",
                incorrect: ["salir / abandonar", "observar / mirar"]
            },
            {
                verb: "go through",
                correct: "pasar por / revisar",
                incorrect: ["evitar / ignorar", "olvidar / perder"]
            },
            {
                verb: "go ahead",
                correct: "continuar / empezar",
                incorrect: ["detenerse / parar", "retroceder / cancelar"]
            },
            {
                verb: "go with",
                correct: "combinar con / acompañar",
                incorrect: ["rechazar / oponerse", "separar / dividir"]
            }
        ];

        let currentQuestion = 0;
        let score = 0;
        let selectedAnswer = null;
        let correctLetter = null;

        // Mostrar la primera pregunta
        showQuestion();

        function showQuestion() {
            if (currentQuestion >= questions.length) {
                endQuiz();
                return;
            }

            const q = questions[currentQuestion];
            document.getElementById("verb").textContent = q.verb;
            document.getElementById("current").textContent = currentQuestion + 1;

            // Mezclar opciones
            const options = [q.correct, ...q.incorrect];
            shuffleArray(options);
            
            // Determinar qué letra es la correcta
            correctLetter = options[0] === q.correct ? 'A' : 
                          (options[1] === q.correct ? 'B' : 'C');

            // Mostrar opciones
            const optionsContainer = document.getElementById("options");
            optionsContainer.innerHTML = `
                <div class="option" onclick="selectOption(this, 'A')">A) ${options[0]}</div>
                <div class="option" onclick="selectOption(this, 'B')">B) ${options[1]}</div>
                <div class="option" onclick="selectOption(this, 'C')">C) ${options[2]}</div>
            `;

            // Reiniciar selección
            document.getElementById("next-btn").disabled = true;
            selectedAnswer = null;
        }

        function selectOption(element, letter) {
            // Deseleccionar otras opciones
            document.querySelectorAll(".option").forEach(opt => {
                opt.classList.remove("selected");
            });

            // Seleccionar esta opción
            element.classList.add("selected");
            selectedAnswer = letter;
            document.getElementById("next-btn").disabled = false;
        }

        function nextQuestion() {
            if (!selectedAnswer) return;

            // Verificar respuesta
            const options = document.querySelectorAll(".option");
            if (selectedAnswer === correctLetter) {
                options.forEach(opt => {
                    if (opt.textContent.startsWith(correctLetter)) {
                        opt.classList.add("correct");
                    }
                });
                score++;
                document.getElementById("score").textContent = score;
            } else {
                options.forEach(opt => {
                    if (opt.textContent.startsWith(selectedAnswer)) {
                        opt.classList.add("incorrect");
                    }
                    if (opt.textContent.startsWith(correctLetter)) {
                        opt.classList.add("correct");
                    }
                });
            }

            // Deshabilitar botón
            document.getElementById("next-btn").disabled = true;

            // Siguiente pregunta después de 1 segundo
            setTimeout(() => {
                currentQuestion++;
                showQuestion();
            }, 1000);
        }

        function endQuiz() {
            alert(`¡Quiz completado!\nPuntaje final: ${score}/20`);
            // Reiniciar quiz
            currentQuestion = 0;
            score = 0;
            document.getElementById("score").textContent = score;
            showQuestion();
        }

        function shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        }
    </script>
</body>
</html>
