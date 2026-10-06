<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Maps</title>
    <!-- 引入 Google Fonts 的芫荽體 (Iansui) -->
    <link href="https://fonts.googleapis.com/css2?family=Iansui&display=swap" rel="stylesheet">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Iansui', sans-serif !important;
        }
        body {
            background-image: url('https://i.meee.com.tw/sqDHUVl.png');
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            color: #ffffff;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 20px;
            position: relative;
            overflow: hidden;
        }
        
        body::before {
            content: '';
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(10, 10, 15, 0.7);
            z-index: -1;
        }

        /* 頂部標題與線條美化（解決重合問題） */
        .top-header {
            position: absolute;
            top: 20px;
            left: 30px;
            font-size: 1.8rem;
            color: #60a5fa;
            width: calc(100% - 60px);
            padding-bottom: 8px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
            letter-spacing: 1px;
        }

        /* 絕對固定的卡片外框 */
        .container {
            text-align: center;
            background: rgba(18, 18, 26, 0.85);
            padding: 25px 30px;
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.1);
            width: 520px;
            height: 420px;
            backdrop-filter: blur(8px);
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            position: relative;
        }

        /* 抽圖時用 display: none 徹底移除佔位，解決按鈕下移與位移問題 */
        .container.hide-ui h1,
        .container.hide-ui .category-buttons {
            display: none !important;
        }

        h1 {
            font-size: 1.7rem;
            letter-spacing: 1px;
            color: #e2e8f0;
            height: 30px;
            line-height: 30px;
        }

        .category-buttons {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }
        .cat-btn {
            background: rgba(255, 255, 255, 0.06);
            color: #cbd5e1;
            border: 1px solid rgba(255, 255, 255, 0.12);
            padding: 7px;
            font-size: 0.95rem;
            border-radius: 6px;
            cursor: pointer;
            transition: all 0.2s;
        }
        .cat-btn:hover {
            background: rgba(255, 255, 255, 0.12);
            color: #fff;
        }
        .cat-btn.active {
            background: #2563eb;
            border-color: #3b82f6;
            color: #fff;
            font-weight: bold;
        }
        
        /* 結果顯示區塊 */
        #result {
            height: 120px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: #fbbf24;
            padding: 0 10px;
            transition: transform 0.3s ease;
        }

        .container.drawing-mode #result {
            transform: scale(1.15);
        }

        .fade-in {
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1); }
        }

        .result-star {
            font-size: 1.1rem;
            color: #60a5fa;
            margin-bottom: 4px;
            font-weight: bold;
            letter-spacing: 2px;
        }
        .result-map {
            font-size: 2rem;
            color: #fbbf24;
            word-break: break-all;
            line-height: 1.2;
        }

        .action-buttons {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .draw-btn {
            background: #2563eb;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 1.05rem;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s;
            width: 100%;
        }
        .draw-btn:hover {
            background: #1d4ed8;
        }

        .list-btn {
            background: rgba(255, 255, 255, 0.08);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.15);
            padding: 8px 15px;
            font-size: 0.95rem;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s;
            width: 100%;
        }
        .list-btn:hover {
            background: rgba(255, 255, 255, 0.15);
        }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.85);
            justify-content: center;
            align-items: center;
            z-index: 10;
        }
        .modal-content {
            background: #181824;
            padding: 30px;
            border-radius: 12px;
            max-width: 550px;
            width: 90%;
            max-height: 80vh;
            overflow-y: auto;
            text-align: left;
            border: 1px solid rgba(255, 255, 255, 0.15);
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.5);
        }
        .modal-content h2 {
            margin-bottom: 20px;
            font-size: 1.6rem;
            color: #fbbf24;
            text-align: center;
        }
        .star-group {
            margin-bottom: 15px;
            background: rgba(255, 255, 255, 0.03);
            padding: 12px 15px;
            border-radius: 8px;
            border-left: 4px solid #3b82f6;
        }
        .star-title {
            font-size: 1.1rem;
            color: #60a5fa;
            margin-bottom: 6px;
            font-weight: bold;
            letter-spacing: 1px;
        }
        .star-maps {
            font-size: 1rem;
            color: #e2e8f0;
            line-height: 1.5;
            word-break: break-all;
        }

        .close-btn {
            margin-top: 20px;
            background: #dc2626;
            color: white;
            border: none;
            padding: 12px;
            width: 100%;
            font-size: 1.1rem;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s;
        }
        .close-btn:hover {
            background: #b91c1c;
        }
    </style>
</head>
<body>

    <div class="top-header">pick-map</div>

    <div class="container" id="mainContainer">
        <h1>Maps</h1>
        
        <div class="category-buttons">
            <button class="cat-btn active" onclick="switchCategory('speedLeague', this)">競速聯賽圖池</button>
            <button class="cat-btn" onclick="switchCategory('speedAll', this)">競速全圖</button>
            <button class="cat-btn" onclick="switchCategory('itemList', this)">道具聯賽圖池</button>
            <button class="cat-btn" onclick="switchCategory('nostalgia', this)">懷舊圖池</button>
        </div>

        <div id="result">點擊下方按鈕抽圖</div>
        
        <div class="action-buttons">
            <button class="draw-btn" onclick="drawMap()">開始抽地圖</button>
            <button class="list-btn" onclick="showMapList()">顯示所有地圖</button>
        </div>
    </div>

    <div id="mapModal" class="modal">
        <div class="modal-content">
            <h2 id="modalTitle">地圖清單</h2>
            <div id="modalStarContainer"></div>
            <button class="close-btn" onclick="closeMapList()">關閉</button>
        </div>
    </div>

    <script>
        const mapPools = {
            speedLeague: {
                name: "競速聯賽圖池",
                pools: [
                    { star: "★★★★★★★", maps: ["極速空港", "山海畫境"] },
                    { star: "★★★★★★", maps: ["美洲大峽谷", "蘇格蘭場"] },
                    { star: "★★★★★", maps: ["秋名山", "莫高窟", "亞特蘭蒂斯", "反向亞特蘭蒂斯", "老街工地", "赤城紅葉", "雪境裂淵", "火星基地", "西部礦山"] },
                    { star: "★★★★", maps: ["西湖", "長城", "1號公路", "TROY-零號試驗場", "千戶苗寨", "疾風機場", "決戰! 雪山之巔", "夢回古蜀", "極速航天城", "神都千古恆照", "流殤曲水", "端午競渡", "泰坦之巔", "雲湧天門", "心淵"] },
                    { star: "★★★", maps: ["11城", "北海漁場", "TROY-熔煉車間", "一路向黔", "阿爾法總部", "決戰! 海濱之眼", "天宮尋夢", "特洛伊環城", "龍晶湖", "絕色江西", "霧山五行", "浪漫海濱", "夜遊瀟湘", "踏雪尋春", "貓夢浮屋", "天工水城"] }
                ]
            },
            speedAll: {
                name: "競速全圖",
                pools: [
                    { star: "★★★★★★★", maps: ["極速空港", "山海畫境"] },
                    { star: "★★★★★★", maps: ["美洲大峽谷", "蘇格蘭場"] },
                    { star: "★★★★★", maps: ["秋名山", "莫高窟", "亞特蘭蒂斯", "反向亞特蘭蒂斯", "哈比人之旅", "老街工地", "赤城紅葉", "雪境裂淵", "火星基地", "沁園春", "天空之城", "西部礦山"] },
                    { star: "★★★★", maps: ["西湖", "長城", "1號公路", "TROY-零號試驗場", "千戶苗寨", "疾風機場", "決戰! 雪山之巔", "夢回古蜀", "極速航天城", "神都千古恒照", "流殤曲水", "端午競渡", "泰坦之巔", "雲湧天門", "新天鵝堡", "秋之物語", "侏羅紀公園", "花落夏海", "綠野逐風", "桃源劍閣", "人魚島探險"] },
                    { star: "★★★", maps: ["11城", "北海漁場", "TROY-熔煉車間", "一路向黔", "阿爾法總部", "決戰! 海濱之眼", "天宮尋夢", "超弦基地", "特洛伊環城", "龍晶湖", "絕色江西", "伊甸掠影", "霧山五行", "浪漫海濱", "夜遊瀟湘", "踏雪尋春", "反向11城", "月光之城", "時之沙", "情迷法蘭西", "廣寒仙境", "城市網咖", "極地冰鎮", "龍門新春", "洛杉磯", "冰雪企鵝島", "星星火車站", "夜鳴沙都", "電音夢工廠", "幻音城假日", "科隆大教堂", "星夢遊樂園", "炎光王城", "戀戀千陽", "雲遊天府", "霧山楓吟", "京華冬夢", "極星幻域", "黃河萬里奔流", "舊夢碼頭", "熔爐角鬥場", "雲夢澤", "一夢青花", "千年絲路", "時光紀念館", "雪地嘉年華", "沉睡森林", "我們戀愛吧", "雪地大冒險", "聆風鎮", "彩虹風車島", "反向彩虹風車島", "羅馬競技場", "極速列車", "燕子塢"] },
                    { star: "★★", maps: ["冰川滑雪場", "馬達加斯加", "法老金字塔", "山雪遊龍", "情迷愛琴海", "鵲橋仙境", "飛馳絲路", "霆城新港", "叢林派對", "序列中樞"] },
                    { star: "★", maps: ["中國城", "老街管道"] }
                ]
            },
            itemList: {
                name: "道具聯賽圖池",
                pools: [
                    { star: "★★★★★", maps: ["天空之城"] },
                    { star: "★★★★", maps: ["1號公路", "桃源劍閣", "新天鵝堡", "千戶苗寨", "疾風機場", "花落夏海", "秋之物語", "神都千古恆照", "極速航天城"] },
                    { star: "★★★", maps: ["希臘神殿", "極地冰鎮", "因特拉肯", "彩虹風車島", "龍門新春", "城市網吧", "廣寒仙境", "香波島", "月光之城", "冰雪企鵝島", "夜鳴沙都", "水行瑪雅", "電音夢工廠", "反向彩虹風車島", "星星火車站", "幻音城假日", "320冒險島", "阿爾法總部", "戀戀千陽", "科隆大教堂", "聆風鎮", "舊夢碼頭", "炎光王城", "霧山楓吟", "京華冬夢"] }
                ]
            },
            nostalgia: {
                name: "懷舊圖池",
                pools: [
                    { star: "★", maps: ["大壩", "狂鯊水世界", "酥脆奶油谷", "繁花巴比倫", "咕嚕星", "玄門幽谷", "英倫古堡", "鴨鴨水樂園", "糖果不夜城", "蜂之蜜語", "糖果樂園", "小豬部落", "失落遺跡"] }
                ]
            }
        };

        let currentCategory = 'speedLeague';
        let isDrawing = false;

        function getAllMapsWithStar(categoryKey) {
            let list = [];
            mapPools[categoryKey].pools.forEach(group => {
                group.maps.forEach(mapName => {
                    list.push({ star: group.star, name: mapName });
                });
            });
            return list;
        }

        function switchCategory(categoryKey, btnElement) {
            if (isDrawing) return;
            currentCategory = categoryKey;
            
            const buttons = document.querySelectorAll('.cat-btn');
            buttons.forEach(btn => btn.classList.remove('active'));
            
            btnElement.classList.add('active');
            
            const resultDiv = document.getElementById("result");
            resultDiv.innerText = "已切換圖池，請抽圖";
            resultDiv.classList.remove("fade-in");
            void resultDiv.offsetWidth;
            resultDiv.classList.add("fade-in");
        }

        function drawMap() {
            if (isDrawing) return;
            isDrawing = true;

            const container = document.getElementById("mainContainer");
            const resultDiv = document.getElementById("result");
            const flatMaps = getAllMapsWithStar(currentCategory);
            
            container.classList.add("hide-ui", "drawing-mode");
            
            let count = 0;
            const interval = setInterval(() => {
                const randomIndex = Math.floor(Math.random() * flatMaps.length);
                const item = flatMaps[randomIndex];
                resultDiv.innerHTML = `<div class="result-star">${item.star}</div><div class="result-map">${item.name}</div>`;
                count++;
                
                if (count > 15) {
                    clearInterval(interval);
                    const finalItem = flatMaps[Math.floor(Math.random() * flatMaps.length)];
                    
                    resultDiv.innerHTML = `<div class="result-star">${finalItem.star}</div><div class="result-map">${finalItem.name}</div>`;
                    
                    setTimeout(() => {
                        container.classList.remove("hide-ui", "drawing-mode");
                        resultDiv.classList.remove("fade-in");
                        void resultDiv.offsetWidth;
                        resultDiv.classList.add("fade-in");
                        isDrawing = false;
                    }, 400);
                }
            }, 60);
        }

        function showMapList() {
            const modal = document.getElementById("mapModal");
            const modalTitle = document.getElementById("modalTitle");
            const container = document.getElementById("modalStarContainer");
            
            const currentPool = mapPools[currentCategory];
            let totalCount = 0;
            currentPool.pools.forEach(g => totalCount += g.maps.length);
            
            modalTitle.innerText = `${currentPool.name}（共 ${totalCount} 張）`;
            
            container.innerHTML = "";
            currentPool.pools.forEach(group => {
                const groupDiv = document.createElement("div");
                groupDiv.className = "star-group";
                
                const titleDiv = document.createElement("div");
                titleDiv.className = "star-title";
                titleDiv.innerText = group.star;
                
                const mapsDiv = document.createElement("div");
                mapsDiv.className = "star-maps";
                mapsDiv.innerText = group.maps.join("、");
                
                groupDiv.appendChild(titleDiv);
                groupDiv.appendChild(mapsDiv);
                container.appendChild(groupDiv);
            });
            
            modal.style.display = "flex";
        }

        function closeMapList() {
            document.getElementById("mapModal").style.display = "none";
        }
    </script>

</body>
</html>
