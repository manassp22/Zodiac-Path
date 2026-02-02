<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Zodiac Path - สถานีพยากรณ์</title>
    <link href="https://fonts.googleapis.com/css2?family=Mitr:wght@300;400;500&family=Sarabun:ital,wght@0,300;0,400;1,300&display=swap" rel="stylesheet">
    <style>
        :root {
            --midnight: #0a041a;
            --royal-gold: linear-gradient(135deg, #bf953f, #fcf6ba, #b38728, #fbf5b7, #aa771c);
            --gold-line: rgba(212, 175, 55, 0.7);
            --glass: rgba(15, 5, 30, 0.92);
        }

        body {
            font-family: 'Mitr', sans-serif;
            background: radial-gradient(circle at center, #1a0633 0%, #05010a 100%);
            background-attachment: fixed;
            color: #e0e0e0;
            margin: 0;
            display: flex;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            width: 100%;
            max-width: 900px;
            background: var(--glass);
            backdrop-filter: blur(25px);
            border: 1px solid var(--gold-line);
            border-radius: 40px;
            padding: 40px;
            text-align: center;
            box-shadow: 0 0 60px rgba(0,0,0,0.9);
            align-self: center;
        }

        h1 { font-size: 3rem; background: var(--royal-gold); -webkit-background-clip: text; -webkit-text-fill-color: transparent; letter-spacing: 5px; }

        /* --- หลังไพ่ลายเส้นราศี (Sacred Zodiac Lines) --- */
        .tarot-card {
            width: 110px;
            height: 185px;
            border-radius: 12px;
            cursor: pointer;
            position: relative;
            background: #0d061f;
            border: 2px solid #bf953f;
            overflow: hidden;
            transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            /* ลายเส้นดาว */
            background-image: radial-gradient(circle, #fff 0.5px, transparent 1px);
            background-size: 15px 15px;
        }

        .tarot-card::before {
            content: "";
            position: absolute;
            top: 50%; left: 50%;
            width: 80px; height: 80px;
            border: 1px double var(--gold-line);
            border-radius: 50%;
            transform: translate(-50%, -50%);
            background: repeating-conic-gradient(from 0deg, transparent 0deg 30deg, rgba(212, 175, 55, 0.1) 30deg 60deg);
        }

        .tarot-card::after {
            content: "✦";
            position: absolute;
            top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            color: #fcf6ba;
            font-size: 1.5rem;
            text-shadow: 0 0 10px #bf953f;
        }

        .tarot-card:hover {
            transform: translateY(-20px) scale(1.05);
            box-shadow: 0 0 30px rgba(191, 149, 63, 0.5);
        }

        .card-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(110px, 1fr));
            gap: 20px;
            margin-top: 30px;
            max-height: 500px;
            overflow-y: auto;
            padding: 20px;
        }

        /* --- ส่วนผลลัพธ์ --- */
        #result-screen { display: none; animation: fadeIn 0.8s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }

        .result-card-img {
            width: 250px;
            border-radius: 15px;
            border: 4px solid #bf953f;
            box-shadow: 0 0 40px rgba(0,0,0,0.8);
            margin-bottom: 25px;
        }

        .prediction-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            text-align: left;
            font-family: 'Sarabun', sans-serif;
        }

        .p-box {
            background: rgba(255, 255, 255, 0.05);
            padding: 20px;
            border-radius: 15px;
            border-left: 4px solid #bf953f;
        }

        .full { grid-column: 1 / -1; background: rgba(191, 149, 63, 0.1); }

        .label { display: block; color: #fcf6ba; font-family: 'Mitr', sans-serif; font-weight: 500; margin-bottom: 5px; }

        .blessing { margin-top: 35px; padding: 20px; font-size: 1.3rem; color: #fcf6ba; font-style: italic; border-top: 1px dashed var(--gold-line); }

        button {
            background: var(--royal-gold);
            color: #1a0633;
            border: none;
            padding: 15px 45px;
            border-radius: 50px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 30px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
    </style>
</head>
<body>

<div class="container">
    <div id="start-screen">
        <h1>Zodiac Path</h1>
        <p>ยินดีต้อนรับสู่เส้นทางแห่งดวงดาว... ค้นหาคำตอบที่ซ่อนอยู่ในใจคุณ</p>
        <button onclick="showPickScreen()">เริ่มต้นทำนาย</button>
    </div>

    <div id="pick-screen" style="display:none;">
        <h2 style="color: #fcf6ba;">ตั้งจิตให้สงบแล้วเลือกไพ่ที่ดึงดูดคุณที่สุด</h2>
        <div class="card-grid" id="card-grid"></div>
    </div>

    <div id="result-screen">
        <h2 id="card-name" style="color:#fcf6ba; font-size: 2rem; margin-bottom: 20px;"></h2>
        <img id="card-img" class="result-card-img" src="" alt="Card Image">
        
        <div class="prediction-grid">
            <div class="p-box"><span class="label">💼 การเรียน / การงาน</span><span id="work"></span></div>
            <div class="p-box"><span class="label">💰 การเงิน</span><span id="money"></span></div>
            <div class="p-box"><span class="label">❤️ ความรัก</span><span id="love"></span></div>
            <div class="p-box"><span class="label">🏥 สุขภาพ</span><span id="health"></span></div>
            <div class="p-box full"><span class="label">💡 คำแนะนำสำหรับคุณ</span><span id="advice"></span></div>
        </div>
        
        <div class="blessing" id="blessing"></div>
        <button onclick="location.reload()">ทำนายอีกครั้ง</button>
    </div>
</div>

<script>
    const tarotDeck = [];

    function initDeck() {
        const majors = ["The Fool", "The Magician", "The High Priestess", "The Empress", "The Emperor", "The Hierophant", "The Lovers", "The Chariot", "Strength", "The Hermit", "Wheel of Fortune", "Justice", "The Hanged Man", "Death", "Temperance", "The Devil", "The Tower", "The Star", "The Moon", "The Sun", "Judgement", "The World"];
        
        // ดึงรูปจาก API แหล่งที่เสถียรที่สุดสำหรับเว็บ
        majors.forEach((name, i) => {
            tarotDeck.push({
                name: name,
                img: `https://raw.githubusercontent.com/ekelen/tarot-api/master/static/cards/m${i.toString().padStart(2, '0')}.jpg`,
                work: "โอกาสใหม่ๆ กำลังก่อตัวขึ้นในสายงานของคุณ ความคิดสร้างสรรค์จะเป็นกุญแจสำคัญสู่ความสำเร็จ",
                money: "การเงินเริ่มขยับเขยื้อนไปในทิศทางที่ดีขึ้น มีเกณฑ์ได้ลาภลอยหรือผลตอบแทนจากสิ่งที่เคยลงทุนไว้",
                love: "เป็นช่วงเวลาแห่งความเข้าใจและเห็นอกเห็นใจกัน คนโสดจะพบคนที่ทำให้หัวใจอบอุ่น",
                health: "สุขภาพแข็งแรงดี ควรหาเวลาพักผ่อนสายตาจากการทำงานบ้างเพื่อรักษาพลังงานบวก",
                advice: "จงเชื่อมั่นในสัญญาณที่จักรวาลส่งมาให้ในวันนี้ ความสำเร็จอยู่ไม่ไกลจากความตั้งใจของคุณ",
                blessing: "✨ ไม่ว่าดวงดาวจะหมุนไปทางใด ขอให้จำไว้ว่าคุณเก่งที่สุดในแบบของคุณ ✨"
            });
        });

        const suits = ['Wands', 'Cups', 'Swords', 'Pentacles'];
        suits.forEach(suit => {
            for (let i = 1; i <= 14; i++) {
                let label = i;
                if(i===1) label="Ace"; if(i===11) label="Page"; if(i===12) label="Knight"; if(i===13) label="Queen"; if(i===14) label="King";
                tarotDeck.push({
                    name: `${label} of ${suit}`,
                    img: `https://raw.githubusercontent.com/ekelen/tarot-api/master/static/cards/${suit.toLowerCase()[0]}${i.toString().padStart(2, '0')}.jpg`,
                    work: `ควรเน้นการสื่อสารและความร่วมมือในที่ทำงาน ผลลัพธ์ที่ดีย่อมมาจากความพยายามที่ต่อเนื่อง`,
                    money: "การเงินไหลเวียนได้ดีขึ้น แต่ควรบริหารจัดการรายจ่ายให้เป็นระเบียบเพื่อความมั่นคงในอนาคต",
                    love: "ความสัมพันธ์เรียบง่ายแต่มั่นคง ลองหาเวลาเล็กๆ น้อยๆ ทำกิจกรรมร่วมกับคนที่คุณรัก",
                    health: "ระวังอาการเมื่อยล้าตามร่างกาย การยืดเส้นยืดสายระหว่างวันจะช่วยได้มาก",
                    advice: "ค่อยเป็นค่อยไป ไม่ต้องรีบร้อน ทุกอย่างมีจังหวะเวลาที่ลงตัวของมันเสมอ",
                    blessing: "🌙 วันนี้คุณทำดีที่สุดแล้ว พักผ่อนให้เต็มที่นะ 🌙"
                });
            }
        });
    }

    function showPickScreen() {
        document.getElementById('start-screen').style.display = 'none';
        document.getElementById('pick-screen').style.display = 'block';
        const grid = document.getElementById('card-grid');
        grid.innerHTML = '';
        for (let i = 0; i < tarotDeck.length; i++) {
            const card = document.createElement('div');
            card.className = 'tarot-card';
            card.onclick = () => showResult();
            grid.appendChild(card);
        }
    }

    function showResult() {
        const card = tarotDeck[Math.floor(Math.random() * tarotDeck.length)];
        document.getElementById('pick-screen').style.display = 'none';
        document.getElementById('result-screen').style.display = 'block';
        
        document.getElementById('card-name').innerText = "ไพ่ของคุณคือ " + card.name;
        document.getElementById('card-img').src = card.img;
        document.getElementById('work').innerText = card.work;
        document.getElementById('money').innerText = card.money;
        document.getElementById('love').innerText = card.love;
        document.getElementById('health').innerText = card.health;
        document.getElementById('advice').innerText = card.advice;
        document.getElementById('blessing').innerText = card.blessing;
    }

    initDeck();
</script>
</body>
</html>
