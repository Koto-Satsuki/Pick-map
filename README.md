<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>遊戲地圖隨機抽取器</title>
    <!-- 引入 Google Fonts 的芫荽體 (Iansui) -->
    <link href="https://fonts.googleapis.com/css2?family=Iansui&display=swap" rel="stylesheet">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            /* 這裡設定你的背景圖片網址。如果想換圖片，把網址換成你的圖片直鏈即可 */
            background-image: url('https://i.meee.com.tw/sqDHUVl.png');
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            
            /* 套用芫荽體，並設定備用字體 */
            font-family: 'Iansui', sans-serif;
            color: #ffffff;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 20px;
            position: relative;
        }
        
        /* 加上一層半透明深色遮罩，讓文字跟介面更清晰易讀 */
        body::before {
            content: '';
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.65);
            z-index: -1;
        }

        .container {
            text-align: center;
            background: rgba(20, 20, 30, 0.85);
            padding: 35px;
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(255, 255, 255, 0.15);
            max-width: 650px;
            width: 100%;
            backdrop-filter: blur(5px);
        }
        h1 {
            margin-bottom: 20px;
            font-size: 2rem;
            letter-spacing: 1px;
            color: #f0f0f0;
        }
        /* 三大分類按鈕排版 */
        .category-buttons {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-bottom: 20px;
        }
        .cat-btn {
            background: rgba(255, 255, 255, 0.08);
            color: #ddd;
            border: 1px solid rgba(255, 255, 255, 0.2);
            padding: 12px;
            font-size: 1.1rem;
            font-family: 'Iansui', sans-serif;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s;
        }
        .cat-btn:hover {
            background: rgba(255, 255, 255, 0.15);
            color: #fff;
        }
        .cat-btn.active {
            background: #2563eb;
            border-color: #3b82f6;
            color: #fff;
            font-weight: bold;
        }
        
        /* 結果顯示框 (拿掉所有發光特效) */
        #result {
            font-size: 2.2rem;
            margin: 20px 0;
            min-height: 70px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #facc15; /* 改用乾淨的溫和黃色 */
            word-break: break-all;
            padding: 0 10px;
        }

        /* 按鈕區群組 */
        .action-buttons {
            display: flex;
            gap: 10px;
        }

        /* 開始抽圖按鈕 */
        .draw-btn {
            background: #2563eb;
            color: white;
            border: none;
            padding: 14px 20px;
            font-size: 1.2rem;
            font-family: 'Iansui', sans-serif;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s;
            flex: 2;
        }
        .draw-btn:hover {
            background: #1d4ed8;
        }

        /* 顯示所有地圖按鈕 */
        .list-btn {
            background: rgba(255, 255, 255, 0.1);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.2);
            padding: 14px 15px;
            font-size: 1.1rem;
            font-family: 'Iansui', sans-serif;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s;
            flex: 1;
        }
        .list-btn:hover {
            background: rgba(255, 255, 255, 0.2);
        }

        /* 彈出視窗（顯示所有地圖的清單） */
        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.8);
            justify-content: center;
            align-items: center;
            z-index: 10;
        }
        .modal-content {
            background: #1f1f2e;
            padding: 30px;
            border-radius: 12px;
            max-width: 500px;
            width: 90%;
            max-height: 80vh;
            overflow-y: auto;
            text-align: left;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        .modal-content h2 {
            margin-bottom: 15px;
            font-size: 1.5rem;
            color: #facc15;
            text-align: center;
        }
        .modal-content ul {
            list-style-type: none;
            padding: 0;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
        }
        .modal-content li {
            background: rgba(255, 255, 255, 0.05);
            padding: 8px 12px;
            border-radius: 6px;
            font-size: 0.95rem;
            color: #e2e8f0;
        }
        .close-btn {
            margin-top: 20px;
            background: #ef4444;
            color: white;
            border: none;
            padding: 10px;
            width: 100%;
            font-family: 'Iansui', sans-serif;
            font-size: 1.1rem;
            border-radius: 8px;
            cursor: pointer;
        }
        .close-btn:hover {
            background: #dc2626;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>🎮 遊戲地圖隨機抽取器</h1>
        
        <!-- 三大圖池切換按鈕 -->
        <div class="category-buttons">
            <button class="cat-btn active" onclick="switchCategory('speedLeague', this)">🏁 競速賽聯賽圖池</button>
            <button class="cat-btn" onclick="switchCategory('speedAll', this)">🌍 競速賽全圖圖池</button>
            <button class="cat-btn" onclick="switchCategory('itemList', this)">🛡️ 道具賽聯賽圖池</button>
        </div>

        <div id="result">請點擊下方按鈕抽圖</div>
        
        <div class="action-buttons">
            <button class="draw-btn" onclick="drawMap()">開始抽地圖</button>
            <button class="list-btn" onclick="showMapList()">顯示所有地圖</button>
        </div>
    </div>

    <!-- 彈出視窗：顯示所有地圖 -->
    <div id="mapModal" class="modal">
        <div class="modal-content">
            <h2 id="modalTitle">地圖清單</h2>
            <ul id="modalMapList"></ul>
            <button class="close-btn" onclick="closeMapList()">關閉</button>
        </div>
    </div>

    <script>
        const mapPools = {
            speedLeague: {
                name: "競速賽聯賽圖池",
                maps: [
                    "極速空港", "山海畫境", "美洲大峽谷", "蘇格蘭場",
                    "秋名山", "莫高窟", "亞特蘭蒂斯", "反向亞特蘭蒂斯", "老街工地", "赤城紅葉", "雪境裂淵", "火星基地", "西部礦山",
                    "西湖", "長城", "1號公路", "TROY-零號試驗場", "千戶苗寨", "疾風機場", "決戰! 雪山之巔", "夢回古蜀", "極速航天城", "神都千古恆照", "流殤曲水", "端午競渡", "泰坦之巔", "雲湧天門", "心淵",
                    "11城", "北海漁場", "TROY-熔煉車間", "一路向黔", "阿爾法總部", "決戰! 海濱之眼", "天宮尋夢", "特洛伊環城", "龍晶湖", "絕色江西", "霧山五行", "浪漫海濱", "夜遊瀟湘", "踏雪尋春", "貓夢浮屋", "天工水城"
                ]
            },
            speedAll: {
                name: "競速賽全圖圖池",
                maps: [
                    "極速空港", "山海畫境", "美洲大峽谷", "蘇格蘭場",
                    "秋名山", "莫高窟", "亞特蘭蒂斯", "反向亞特蘭蒂斯", "哈比人之旅", "老街工地", "赤城紅葉", "雪境裂淵", "火星基地", "沁園春", "天空之城", "西部礦山",
                    "西湖", "長城", "1號公路", "TROY-零號試驗場", "千戶苗寨", "疾風機場", "決戰! 雪山之巔", "夢回古蜀", "極速航天城", "神都千古恒照", "流殤曲水", "端午競渡", "泰坦之巔", "雲湧天門", "新天鵝堡", "秋之物語", "侏羅紀公園", "花落夏海", "綠野逐風", "桃源劍閣", "人魚島探險",
                    "11城", "北海漁場", "TROY-熔煉車間", "一路向黔", "阿爾法總部", "決戰! 海濱之眼", "天宮尋夢", "超弦基地", "特洛伊環城", "龍晶湖", "絕色江西", "伊甸掠影", "霧山五行", "浪漫海濱", "夜遊瀟湘", "踏雪尋春", "反向11城", "月光之城", "時之沙", "情迷法蘭西", "廣寒仙境", "城市網咖", "極地冰鎮", "龍門新春", "洛杉磯", "冰雪企鵝島", "星星火車站", "夜鳴沙都", "電音夢工廠", "幻音城假日", "科隆大教堂", "星夢遊樂園", "炎光王城", "戀戀千陽", "雲遊天府", "霧山楓吟", "京華冬夢", "極星幻域", "黃河萬里奔流", "舊夢碼頭", "熔爐角鬥場", "雲夢澤", "一夢青花", "千年絲路", "時光紀念館", "雪地嘉年華", "沉睡森林", "我們戀愛吧", "雪地大冒險", "聆風鎮", "彩虹風車島", "反向彩虹風車島", "羅馬競技場", "極速列車", "燕子塢",
                    "冰川滑雪場", "馬達加斯加", "法老金字塔", "山雪遊龍", "情迷愛琴海", "鵲橋仙境", "飛馳絲路", "霆城新港", "叢林派對", "序列中樞",
                    "中國城", "老街管道",
                    "大壩", "狂鯊水世界", "酥脆奶油谷", "繁花巴比倫", "咕嚕星", "玄門幽谷", "英倫古堡", "鴨鴨水樂園", "糖果不夜城", "蜂之蜜語", "糖果樂園", "小豬部落", "失落遺跡"
                ]
            },
            itemList: {
                name: "道具賽聯賽圖池",
                maps: [
                    "天空之城",
                    "1號公路", "桃源劍閣", "新天鵝堡", "千戶苗寨", "疾風機場", "花落夏海", "秋之物語", "神都千古恆照", "極速航天城",
                    "希臘神殿", "極地冰鎮", "因特拉肯", "彩虹風車島", "龍門新春", "城市網吧", "廣寒仙境", "香波島", "月光之城", "冰雪企鵝島", "夜鳴沙都", "水行瑪雅", "電音夢工廠", "反向彩虹風車島", "星星火車站", "幻音城假日", "320冒險島", "阿爾法總部", "戀戀千陽", "科隆大教堂", "聆風鎮", "舊夢碼頭", "炎光王城", "霧山楓吟", "京華冬夢"
                ]
            }
        };

        let currentCategory = 'speedLeague';

        function switchCategory(categoryKey, btnElement) {
            currentCategory = categoryKey;
            
            const buttons = document.querySelectorAll('.cat-btn');
            buttons.forEach(btn => btn.classList.remove('active'));
            
            btnElement.classList.add('active');
            document.getElementById("result").innerText = "已切換圖池，請抽圖！";
        }

        function drawMap() {
            const resultDiv = document.getElementById("result");
            const activeMaps = mapPools[currentCategory].maps;
            
            let count = 0;
            const interval = setInterval(() => {
                const randomIndex = Math.floor(Math.random() * activeMaps.length);
                resultDiv.innerText = activeMaps[randomIndex];
                count++;
                
                if (count > 12) {
                    clearInterval(interval);
                    const finalIndex = Math.floor(Math.random() * activeMaps.length);
                    resultDiv.innerText = activeMaps[finalIndex];
                }
            }, 50);
        }

        // 顯示所有地圖的彈跳視窗
        function showMapList() {
            const modal = document.getElementById("mapModal");
            const modalTitle = document.getElementById("modalTitle");
            const modalMapList = document.getElementById("modalMapList");
            
            const currentPool = mapPools[currentCategory];
            modalTitle.innerText = `${currentPool.name}（共 ${currentPool.maps.length} 張）`;
            
            modalMapList.innerHTML = "";
            currentPool.maps.forEach(map => {
                const li = document.createElement("li");
                li.innerText = map;
                modalMapList.appendChild(li);
            });
            
            modal.style.display = "flex";
        }

        function closeMapList() {
            document.getElementById("mapModal").style.display = "none";
        }
    </script>

</body>
</html>
