# karumach.github.io
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FullArt</title>
    <style>
        :root {
            /* Переменные для координат (обновляются через JS) */
            --mx: 50%;
            --my: 50%;
            --rx: 0deg;
            --ry: 0deg;
        }

        body {
            /* ФОН СТРАНИЦЫ: */
            background-image: url(../'фон.jpg');
            background-size: cover;
            background-position: center;
            background-color: #0e0e10; 
            height: 100vh;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
            color: white;
        }

 #play-btn {
    opacity: 0;
    width: 764px; 
    height: 763px;
   
    border: none;
    background-color: transparent;
    
  
    background-image: url('кнопка копия.png');
    background-size: contain;
    background-position: center;
    background-repeat: no-repeat;
    
    cursor: pointer;
    transition: opacity 2s ease, transform 0.3s ease, filter 0.3s;
    
    text-indent: -9999px; 
}

#play-btn.visible { 
    opacity: 1; 
}

#play-btn:hover {
    /* Вместо изменения цвета фона добавим легкое свечение или увеличение */
    filter: brightness(1.2) drop-shadow(0 0 10px rgba(103, 193, 245, 0.8));
    transform: scale(1.05);
}

        /* Отсчет */
        #countdown {
            display: none;
            font-size: 80px;
            font-weight: bold;
            color: #67c1f5;
        }

        /* =========================================
           STEAM CARD EFFECT
        ========================================= */

        .card-container {
            display: none;
            width: 300px;
            height: 450px;
            perspective: 1000px; /* Создает 3D пространство */
        }

        .card-inner {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d;
            /* Используем переменные из JS для наклона */
            transform: rotateX(var(--rx)) rotateY(var(--ry));
            transition: transform 0.1s ease-out;
        }

        /* Когда карта переворачивается */
        .card-container.flipped .card-inner {
            transform: rotateY(180deg) rotateX(0deg) !important;
            transition: transform 0.6s cubic-bezier(0.23, 1, 0.32, 1);
        }

        .card-face {
            width: 100%;
            height: 100%;
            position: absolute;
            backface-visibility: hidden;
            border-radius: 12px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            overflow: hidden;
            box-shadow: 0 20px 40px rgba(0,0,0,0.6);
        }

        .card-front {
            /* ТВОЯ КАРТИНКА РУБАШКИ: */
            background-image: url(../'твой_путь_к_рубашке.jpg');
            background-size: cover;
            background-color: #222;
        }

        .card-back {
            /* ТВОЯ КАРТИНКА ЛИЦЕВОЙ СТОРОНЫ: */
            background-image: url('твой_путь_к_лицу_карты.jpg');
            background-size: cover;
            background-color: #333;
            transform: rotateY(180deg);
        }

        /* ТОТ САМЫЙ ОТЛИВ (Оверлей) */
        .card-face::after {
            content: "";
            position: absolute;
            inset: 0;
            /* Радужный блик, который двигается за мышкой */
            background: linear-gradient(
                125deg,
                rgba(255,255,255,0) 0%,
                rgba(255,255,255,0) 30%,
                rgba(255,255,255,0.1) 40%,
                rgba(255,255,255,0.4) 50%,
                rgba(255,255,255,0.1) 60%,
                rgba(255,255,255,0) 70%,
                rgba(255,255,255,0) 100%
            ),
            radial-gradient(
                circle at var(--mx) var(--my),
                rgba(255,255,255,0.2) 0%,
                rgba(255,255,255,0) 60%
            );
            /* Смещение градиента в зависимости от мышки */
            background-position: calc(var(--mx) * 0.5) calc(var(--my) * 0.5);
            background-size: 200% 200%;
            mix-blend-mode: overlay;
            opacity: 0;
            transition: opacity 0.3s;
        }

        .card-container:hover .card-face::after {
            opacity: 1;
        }

    </style>
</head>
<body>

    <button id="play-btn">FullArt</button>
    <div id="countdown">3</div>

    <div class="card-container" id="card">
        <div class="card-inner">
            <div class="card-face card-front"></div>
            <div class="card-face card-back"></div>
        </div>
    </div>

    <script>
        const playBtn = document.getElementById('play-btn');
        const countdownEl = document.getElementById('countdown');
        const card = document.getElementById('card');
        const cardInner = card.querySelector('.card-inner');

        // Появление кнопки
        setTimeout(() => playBtn.classList.add('visible'), 500);

        // Логика кнопки
        playBtn.addEventListener('click', () => {
            playBtn.style.display = 'none';
            countdownEl.style.display = 'block';
            let count = 3;
            countdownEl.innerText = count;

            const timer = setInterval(() => {
                count--;
                if (count <= 0) {
                    clearInterval(timer);
                    countdownEl.style.display = 'none';
                    card.style.display = 'block';
                } else {
                    countdownEl.innerText = count;
                }
            }, 800);
        });

        // ПОВОРОТ ПО КЛИКУ
        card.addEventListener('click', () => {
            card.classList.toggle('flipped');
        });

        // STEAM EFFECT LOGIC
        card.addEventListener('mousemove', (e) => {
            // Если карта в процессе переворота, не наклоняем
            if (card.classList.contains('flipped')) return;

            const rect = card.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;

            // Вычисляем проценты для градиента
            const xPct = (x / rect.width) * 100;
            const yPct = (y / rect.height) * 100;

            // Вычисляем градусы для наклона (Steam обычно наклоняется плавно, до 15-20 градусов)
            const rx = ((y / rect.height) - 0.5) * -25;
            const ry = ((x / rect.width) - 0.5) * 25;

            // Передаем значения в CSS
            card.style.setProperty('--mx', `${xPct}%`);
            card.style.setProperty('--my', `${yPct}%`);
            card.style.setProperty('--rx', `${rx}deg`);
            card.style.setProperty('--ry', `${ry}deg`);
        });

        // Сброс при уходе мыши
        card.addEventListener('mouseleave', () => {
            card.style.setProperty('--rx', `0deg`);
            card.style.setProperty('--ry', `0deg`);
        });
    </script>
</body>
</html> 
