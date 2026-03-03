# franzoesische.revolution.index.html
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Echo der Guillotine | Die Revolution</title>
    
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@700&family=Quicksand:wght@400;600&display=swap" rel="stylesheet">

    <style>
        :root {
            --blue: #002395;
            --white: #ffffff;
            --red: #ed2939;
            --gold: #d4af37;
            --dark: #0a0a0a;
        }

        /* Grundlegendes Setup - Verhindert Scroll-Fehler */
        * { box-sizing: border-box; }

        body, html {
            margin: 0;
            padding: 0;
            min-height: 100vh;
            font-family: 'Quicksand', sans-serif;
            background-color: var(--dark);
            color: var(--white);
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        /* Hintergrund-System */
        #bg-layer {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            z-index: -2;
            background: linear-gradient(135deg, #1a1a1a 0%, #050505 100%);
            background-size: cover;
            background-position: center;
            transition: background-image 1s ease-in-out;
        }

        .overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.75);
            z-index: -1;
        }

        /* Spiel-Container */
        .game-wrapper {
            width: 95%;
            max-width: 800px;
            margin: 20px auto;
            padding: 25px;
            background: rgba(20, 20, 20, 0.9);
            border: 1px solid var(--gold);
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            /* Ermöglicht langes Scrollen innerhalb des Containers auf kleinen Bildschirmen */
        }

        /* Status-Leiste */
        .stats {
            display: flex;
            justify-content: space-between;
            border-bottom: 1px solid #444;
            padding-bottom: 15px;
            margin-bottom: 20px;
            font-weight: 600;
            color: var(--gold);
            font-size: 0.9rem;
        }

        h1 {
            font-family: 'Cinzel', serif;
            font-size: clamp(1.5rem, 5vw, 2.2rem);
            margin: 10px 0;
            color: var(--white);
            text-align: center;
        }

        #story-text {
            font-size: clamp(1rem, 4vw, 1.2rem);
            line-height: 1.6;
            margin: 25px 0;
            min-height: 100px;
        }

        /* Buttons - Optimiert für Touch & Desktop */
        .choice-list {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .btn {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(212, 175, 55, 0.5);
            color: white;
            padding: 16px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1rem;
            font-family: inherit;
            transition: all 0.2s ease;
            text-align: left;
            text-decoration: none;
        }

        .btn:hover {
            background: var(--gold);
            color: black;
            border-color: var(--gold);
            transform: translateY(-2px);
        }

        .btn:active { transform: translateY(0); }

        /* Fortschrittsbalken */
        .progress-box {
            width: 100%;
            height: 4px;
            background: #222;
            margin-top: 30px;
            border-radius: 2px;
            overflow: hidden;
        }
        #progress-bar {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, var(--blue), var(--red));
            transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* Footer */
        footer {
            margin: 20px 0;
            font-size: 0.8rem;
            color: #666;
            text-align: center;
        }
    </style>
</head>
<body>

<div id="bg-layer"></div>
<div class="overlay"></div>

<div class="game-wrapper">
    <div class="stats">
        <span>Jahr: <span id="year-display">1789</span></span>
        <span>Ansehen: <span id="rep-display">50</span>%</span>
    </div>

    <h1 id="title-display">Revolution</h1>
    <p id="story-text">Lade Szenario...</p>

    <div id="choices" class="choice-list"></div>

    <div class="progress-box">
        <div id="progress-bar"></div>
    </div>
</div>

<footer>
    &copy; 1789 - Das Volk von Frankreich | Erstellt mit ❤️ für die Freiheit
</footer>

<script>
    const gameData = {
        start: {
            title: "Der Ballhausschwur",
            year: 1789,
            text: "Du stehst vor der verschlossenen Tür des Sitzungssaals. Die Abgeordneten des Dritten Standes sind wütend. Jemand schlägt vor, in die nahegelegene Tennishalle auszuweichen. Wirst du den Schwur leisten, eine Verfassung zu erzwingen?",
            image: "bilder/ballhaus.jpg",
            options: [
                { text: "„Wir schwören, uns niemals zu trennen!“", next: "bastille", rep: 20, prog: 15 },
                { text: "Geh leise weg. Der König wird Soldaten schicken.", next: "prison", rep: -30, prog: 10 }
            ]
        },
        bastille: {
            title: "Sturm auf die Bastille",
            year: 1789,
            text: "Paris kocht! Das Volk braucht Pulver für seine Gewehre. Die Bastille, das verhasste Staatsgefängnis, liegt vor euch. Die Kanonen der Festung zielen auf die Menge.",
            image: "bilder/bastille.jpg",
            options: [
                { text: "Stürme voran! Befreit die Gefangenen!", next: "rights", rep: 30, prog: 30 },
                { text: "Versuche, die Menge zu beruhigen – Gewalt ist keine Lösung.", next: "failed", rep: -10, prog: 25 }
            ]
        },
        rights: {
            title: "Menschenrechte",
            year: 1789,
            text: "Die Nationalversammlung hat die Menschenrechte verkündet. 'Freiheit, Gleichheit, Brüderlichkeit!' Aber der König weigert sich in Versailles, Brot nach Paris zu schicken.",
            image: "bilder/rechte.jpg",
            options: [
                { text: "Marschiere mit den Frauen nach Versailles!", next: "flight", rep: 25, prog: 50 },
                { text: "Bleib in Paris und diskutiere über die Gesetze.", next: "flight", rep: 5, prog: 45 }
            ]
        },
        flight: {
            title: "Die Flucht des Königs",
            year: 1791,
            text: "König Ludwig XVI. wurde bei dem Versuch erwischt, ins Ausland zu fliehen. Er wird unter Bewachung nach Paris zurückgebracht. Was soll mit ihm geschehen?",
            image: "bilder/varennes.jpg",
            options: [
                { text: "Er ist ein Verräter! Ruft die Republik aus.", next: "terror", rep: 40, prog: 70 },
                { text: "Er muss unser König bleiben, aber ohne Macht.", next: "guillotine", rep: -50, prog: 60 }
            ]
        },
        terror: {
            title: "Die Schreckensherrschaft",
            year: 1793,
            text: "Robespierre kontrolliert Paris. Überall stehen Guillotinen. Jedes falsche Wort kann dich den Kopf kosten. Wirst du deine Nachbarn denunzieren, um Loyalität zu beweisen?",
            image: "bilder/terror.jpg",
            options: [
                { text: "Beweise deine Treue zur Revolution.", next: "napoleon", rep: 10, prog: 85 },
                { text: "Sprich dich gegen das Morden aus.", next: "guillotine", rep: 100, prog: 80 }
            ]
        },
        napoleon: {
            title: "Der General",
            year: 1799,
            text: "Die Revolution ist im Chaos versunken. Ein junger General namens Napoleon Bonaparte kehrt aus Ägypten zurück und verspricht Ordnung durch Stärke.",
            image: "bilder/napoleon.jpg",
            options: [
                { text: "Unterstütze seinen Staatsstreich.", next: "victory", rep: 50, prog: 100 },
                { text: "Kämpfe für die verlorene Demokratie.", next: "exile", rep: -20, prog: 100 }
            ]
        },
        // Enden
        victory: { title: "Kaiserreich", year: 1804, text: "Du hast überlebt. Frankreich ist ein Weltreich unter Napoleon. Die Freiheit ist verloren, aber der Ruhm ist gewaltig.", options: [], image: "bilder/napoleon.jpg" },
        guillotine: { title: "Das Ende", year: 1794, text: "Das scharfe Beil der Guillotine beendet deine Reise. Die Revolution frisst ihre Kinder.", options: [], image: "bilder/terror.jpg" },
        exile: { title: "Verbannung", year: 1815, text: "Du verbringst deine letzten Tage im Exil. Die Welt hat sich verändert, aber deine Träume blieben unerfüllt.", options: [], image: "bilder/ende_tod.jpg" },
        prison: { title: "Vergessen", year: 1789, text: "Du wurdest in den Kerker geworfen. Während die Welt draußen brennt, bleibst du in der Dunkelheit.", options: [], image: "bilder/ende_tod.jpg" },
        failed: { title: "Gefallen", year: 1789, text: "Du wurdest im Getümmel vor der Bastille tödlich verwundet. Dein Name wird vergessen werden.", options: [], image: "bilder/bastille.jpg" }
    };

    let reputation = 50;

    function renderScene(key) {
        const scene = gameData[key];
        if(!scene) return;

        // UI-Elemente
        document.getElementById('title-display').innerText = scene.title;
        document.getElementById('story-text').innerText = scene.text;
        document.getElementById('year-display').innerText = scene.year;
        document.getElementById('rep-display').innerText = reputation;

        // Bild-Update mit Fallback
        const bg = document.getElementById('bg-layer');
        const img = new Image();
        img.src = scene.image;
        img.onload = () => bg.style.backgroundImage = `url('${scene.image}')`;
        img.onerror = () => bg.style.backgroundImage = "linear-gradient(45deg, #002395, #ed2939)";

        // Buttons
        const choiceContainer = document.getElementById('choices');
        choiceContainer.innerHTML = '';

        scene.options.forEach(opt => {
            const b = document.createElement('button');
            b.className = 'btn';
            b.innerText = opt.text;
            b.onclick = () => {
                reputation += opt.rep;
                document.getElementById('progress-bar').style.width = (opt.prog || 0) + "%";
                renderScene(opt.next);
                window.scrollTo({ top: 0, behavior: 'smooth' }); // Scrollt nach oben bei neuer Szene
            };
            choiceContainer.appendChild(b);
        });

        // Neustart-Option
        if(scene.options.length === 0) {
            const rb = document.createElement('button');
            rb.className = 'btn';
            rb.style.textAlign = "center";
            rb.style.marginTop = "20px";
            rb.innerText = "🔄 Zurück zum Anfang";
            rb.onclick = () => location.reload();
            choiceContainer.appendChild(rb);
        }
    }

    // Start
    window.onload = () => renderScene('start');
</script>
</body>
</html>
