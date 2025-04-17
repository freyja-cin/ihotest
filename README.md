<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>艾禾生醫｜保健問卷推薦系統</title>
  <style>
    body {
      font-family: 'Noto Sans TC', sans-serif;
      background-color: #f9fbf7;
      color: #2f3e2f;
      margin: 0;
      padding: 0;
    }

    .container {
      max-width: 800px;
      margin: 40px auto;
      padding: 24px;
      background: #ffffff;
      border-radius: 12px;
      box-shadow: 0 4px 20px rgba(0,0,0,0.05);
    }

    h1, h2, h3 {
      color: #4e7346;
      text-align: center;
    }

    .progress-container {
      width: 100%;
      height: 8px;
      background-color: #e2e8dc;
      border-radius: 4px;
      overflow: hidden;
      margin: 20px 0;
    }

    .progress-bar {
      height: 100%;
      width: 0%;
      background-color: #a2d49d;
      transition: width 0.3s ease;
    }

    .question {
      margin-bottom: 24px;
    }

    .option {
      background: #f0f6ef;
      border: 1px solid #cdddc6;
      padding: 12px 16px;
      border-radius: 8px;
      margin-bottom: 10px;
      display: block;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    .option:hover {
      background-color: #e8f3e3;
    }

    .option input {
      margin-right: 10px;
    }

    .button-group {
      text-align: center;
      margin-top: 30px;
    }

    button {
      padding: 12px 24px;
      background-color: #7cbf6d;
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      cursor: pointer;
      margin: 0 10px;
      transition: background 0.2s ease;
    }

    button:hover {
      background-color: #5ea955;
    }

    .hidden {
      display: none;
    }

    .result-card {
      background: #f6faf4;
      border: 1px solid #d6e5d2;
      border-radius: 10px;
      padding: 16px;
      margin-bottom: 16px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.05);
    }

    .card-list {
      display: grid;
      gap: 16px;
      grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
      margin-top: 16px;
    }

    #results p {
      text-align: center;
      font-size: 18px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>艾禾生醫｜保健推薦問卷</h1>

    <div class="progress-container">
      <div class="progress-bar" id="progressBar"></div>
    </div>

    <div id="quiz"></div>

    <div class="button-group">
      <button id="nextBtn">下一題</button>
      <button id="submitBtn" class="hidden">查看推薦結果</button>
    </div>

    <div id="results" class="hidden">
      <h2>推薦產品</h2>
      <div id="priority-section">
        <h3>優先推薦（主推產品）</h3>
        <div class="card-list" id="priority-list"></div>
        <p><strong>主推總金額：</strong><span id="priority-total">0</span></p>
      </div>
      <div id="optional-section">
        <h3>加購推薦</h3>
        <div class="card-list" id="optional-list"></div>
      </div>
      <p><strong>總推薦金額：</strong><span id="all-total">0</span></p>
    </div>
    <div class="button-group" style="margin-top: 40px;">
</div>

  </div>

  <script>
 const quizData = [
  {
    title: "1️⃣ 請問您的性別是？",
    type: "radio",
    options: [
      { label: "男性", scores: [] },
      { label: "女性", scores: [] }
    ]
  },
  {
    title: "2️⃣ 您的年齡區間是？",
    type: "radio",
    options: [
      { label: "18–25 歲", scores: [] },
      { label: "26–35 歲", scores: [] },
      { label: "36–45 歲", scores: [] },
      { label: "46–60 歲", scores: [] },
      { label: "60 歲以上", scores: [] }
    ]
  },
  {
    title: "3️⃣ 您的作息通常是？",
    type: "radio",
    options: [
      { label: "早睡早起規律型", scores: [] },
      { label: "熬夜加班、作息不固定", scores: ["B1+2", "B2+2", "Q10+1", "C1+1"] },
      { label: "白天有精神，晚上容易累", scores: ["B1+1", "F1+1", "Q10+1"] },
      { label: "白天精神差", scores: ["B1+2", "P1+1", "F1+1"] }
    ]
  },
  {
    title: "4️⃣ 最近是否經常出現下列情況？（可複選）",
    type: "checkbox",
    options: [
      { label: "起床時仍感疲累", scores: ["B1+1", "B2+1"] },
      { label: "工作中常常感到疲憊", scores: ["B1+2", "F1+1"] },
      { label: "晚上睡眠品質不好", scores: ["Q10+1", "F1+1"] },
      { label: "下午常想睡覺", scores: ["P1+1", "B1+1"] }
    ]
  },
  {
    title: "5️⃣ 您平時的飲食型態是？",
    type: "radio",
    options: [
      { label: "自己煮", scores: [] },
      { label: "外食族", scores: ["S1+2", "F1+1", "C1+1"] },
      { label: "飲食控制中", scores: ["P1+1", "S2+1"] },
      { label: "常常亂吃", scores: ["P1+2", "S2+1"] }
    ]
  },
  {
    title: "6️⃣ 您目前對體態的感覺是？",
    type: "radio",
    options: [
      { label: "滿意現況", scores: [] },
      { label: "想瘦但沒開始", scores: ["P1+2", "S1+1"] },
      { label: "已經在減重", scores: ["S2+2", "P1+1"] },
      { label: "體重常忽胖忽瘦", scores: ["S2+2", "Q10+1"] }
    ]
  },
  {
    title: "7️⃣ 最近是否有以下狀況？（可複選）",
    type: "checkbox",
    options: [
      { label: "手腳冰冷", scores: ["E1+2"] },
      { label: "容易頭暈、氣色差", scores: ["B2+2", "Q10+1"] },
      { label: "用眼過度、眼睛乾澀", scores: ["L1+2"] },
      { label: "久坐久站後下肢腫脹", scores: ["E1+2", "F1+1"] },
      { label: "情緒起伏大、壓力大", scores: ["F1+2", "Q10+1"] },
      { label: "常常腸胃不適、容易脹氣", scores: ["J1+2", "F1+1"] },
      { label: "排便不順、便秘困擾", scores: ["J1+2"] },
      { label: "肌肉痠痛或運動後恢復慢", scores: ["F1+2", "B1+1"] }
    ]
  },
  {
    title: "8️⃣ 家族中是否有疾病史？（可複選）",
    type: "checkbox",
    options: [
      { label: "三高", scores: ["F1+2", "E1+1"] },
      { label: "心血管疾病", scores: ["E1+2", "F1+1"] },
      { label: "糖尿病", scores: ["F1+1", "S1+1"] },
      { label: "無明顯病史", scores: [] }
    ]
  },
  {
    title: "9️⃣ 您目前有在服用保健食品嗎？",
    type: "radio",
    options: [
      { label: "有，固定補充", scores: ["Q10+1", "F1+1"] },
      { label: "偶爾想到才吃", scores: ["B1+1", "B2+1", "C1+1"] },
      { label: "沒有，但有興趣開始", scores: ["P1+1", "B2+1"] }
    ]
  },
  {
    title: "🔟 您目前最在意的健康／保養目標是？（可複選）",
    type: "checkbox",
    options: [
      { label: "提升精神與體力", scores: ["B1+2", "Q10+1"] },
      { label: "改善氣色、紅潤膚色", scores: ["B2+2", "Q10+1"] },
      { label: "促進代謝與排便順暢", scores: ["S2+2", "P1+1"] },
      { label: "體態管理、想變瘦", scores: ["S1+2", "P1+1"] },
      { label: "保護眼睛、抗藍光", scores: ["L1+2"] },
      { label: "穩定膚況、改善敏感", scores: ["C1+2", "Q10+1"] }
    ]
  },
  {
    title: "11️⃣ 最近是否有以下肌膚困擾？（可複選）",
    type: "checkbox",
    options: [
      { label: "氣色蠟黃、膚色不均", scores: ["B2+2", "Q10+1"] },
      { label: "熬夜後爆痘、膚況差", scores: ["B1+1", "F1+1", "C1+2"] },
      { label: "肌膚敏感、泛紅、容易過敏", scores: ["F1+1", "E1+1", "C1+2"] },
      { label: "毛孔粗大、膚色暗沉", scores: ["P1+1", "Q10+1", "C1+2"] },
      { label: "膚況穩定，但希望更透亮", scores: ["C1+1", "B2+1"] }
    ]
  },
  {
    title: "12️⃣ 平時會怎麼保養肌膚？",
    type: "radio",
    options: [
      { label: "幾乎不保養", scores: ["C1+1", "B2+1"] },
      { label: "基本保濕", scores: ["C1+2"] },
      { label: "有敷面膜、定期保養", scores: ["C1+2", "Q10+1"] },
      { label: "肌膚敏感，只敢用溫和產品", scores: ["C1+2", "F1+1"] }
    ]
  },
  {
    title: "13️⃣ 哪類營養攝取可能不足？",
    type: "radio",
    options: [
      { label: "綠色蔬菜", scores: ["J1+2", "F1+1"] },
      { label: "水果", scores: ["J1+2"] },
      { label: "有吃但量不足", scores: ["J1+1"] },
      { label: "都很均衡", scores: [] }
    ]
  },
  {
    title: "14️⃣ 最近是否出現以下情況？（可複選）",
    type: "checkbox",
    options: [
      { label: "一感冒就拖很久才好", scores: ["D1+2", "F1+1"] },
      { label: "小孩或長輩也常生病", scores: ["D1+2"] },
      { label: "換季易過敏、卡喉嚨", scores: ["D1+1", "F1+1"] },
      { label: "想提升自身與家人的防護力", scores: ["D1+2", "Q10+1"] }
    ]
  }
];

    const products = {
        B1: {
             name: "活力戰神B群",
             price: 980,
             recommendedQty: 2,
             image: "https://images.unsplash.com/photo-1627308595229-7830a5c91f9f?auto=format&fit=crop&w=600"
           },
           B2: {
             name: "美顏紅潤B群",
             price: 920,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1606112219348-204d7d8b94ee?auto=format&fit=crop&w=600"
           },
           P1: {
             name: "啵啵飲（代餐）",
             price: 850,
             recommendedQty: 2,
             image: "https://images.unsplash.com/photo-1598514982901-8f332d1c1e4b?auto=format&fit=crop&w=600"
           },
           S1: {
             name: "纖姿茶花",
             price: 790,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1590080876218-9c57d0caa9e4?auto=format&fit=crop&w=600"
           },
           S2: {
             name: "孅逸鞘蕊花",
             price: 890,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1556228578-4c4c8e4bda36?auto=format&fit=crop&w=600"
           },
           E1: {
             name: "蚓激酶",
             price: 1080,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1596814361571-3c2cc5e5b3d0?auto=format&fit=crop&w=600"
           },
           F1: {
             name: "魚油",
             price: 950,
             recommendedQty: 2,
             image: "https://images.unsplash.com/photo-1585912270032-8f1c1bfb2eb1?auto=format&fit=crop&w=600"
           },
           L1: {
             name: "葉黃素",
             price: 880,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1586201375761-83865001e17b?auto=format&fit=crop&w=600"
           },
           C1: {
             name: "外泌體面膜",
             price: 1280,
             recommendedQty: 2,
             image: "https://images.unsplash.com/photo-1612004027753-7bbf38291759?auto=format&fit=crop&w=600"
           },
           Q10: {
             name: "輔酵素Q10（功能性附加）",
             price: 990,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1583337130417-3346a1fcdf42?auto=format&fit=crop&w=600"
           },
           D1: {
             name: "防禦盾β",
             price: 1150,
             recommendedQty: 1,
             image: "https://images.unsplash.com/photo-1611691544918-56a9830e6d23?auto=format&fit=crop&w=600"
           },
           J1: {
             name: "沙拉飲",
             price: 860,
             recommendedQty: 2,
             image: "https://images.unsplash.com/photo-1587502536263-9298c3709943?auto=format&fit=crop&w=600"
           }
         };


    let current = 0;
    const answers = [];
    const scores = {};

    const quizEl = document.getElementById("quiz");
    const nextBtn = document.getElementById("nextBtn");
    const submitBtn = document.getElementById("submitBtn");
    const resultsEl = document.getElementById("results");
    const priorityList = document.getElementById("priority-list");
    const optionalList = document.getElementById("optional-list");
    const priorityTotal = document.getElementById("priority-total");
    const allTotal = document.getElementById("all-total");
    const progressBar = document.getElementById("progressBar");

    function renderQuestion() {
      const q = quizData[current];
      let html = `<div class="question"><h2>${q.title}</h2>`;
      q.options.forEach((opt, i) => {
        html += `
          <label class="option">
            <input type="${q.type}" name="q${current}" value="${i}" />
            ${opt.label}
          </label>`;
      });
      html += `</div>`;
      quizEl.innerHTML = html;

      const percent = (current / quizData.length) * 100;
      progressBar.style.width = `${percent}%`;
    }

    function collectAnswers() {
      const q = quizData[current];
      const inputs = document.querySelectorAll(`input[name="q${current}"]:checked`);
      if (inputs.length === 0) {
        alert("請選擇至少一個選項");
        return false;
      }
      answers[current] = Array.from(inputs).map(i => parseInt(i.value));
      return true;
    }

    function calculateScores() {
      quizData.forEach((q, i) => {
        const selected = answers[i] || [];
        selected.forEach(index => {
          const option = q.options[index];
          (option.scores || []).forEach(score => {
            const [code, val] = score.split("+");
            scores[code] = (scores[code] || 0) + parseInt(val);
          });
        });
      });
    }

    function renderResults() {
      calculateScores();
      quizEl.classList.add("hidden");
      resultsEl.classList.remove("hidden");
      nextBtn.classList.add("hidden");
      submitBtn.classList.add("hidden");

      const priority = [];
      const optional = [];
      let total = 0;
      let priorityTotalVal = 0;

      Object.entries(scores).forEach(([code, score]) => {
        const product = products[code];
        if (!product || score < 1) return;
        const subtotal = product.price * product.recommendedQty;
        total += subtotal;
        if (score >= 4) {
          priority.push({ ...product, subtotal });
          priorityTotalVal += subtotal;
        } else if (score >= 2) {
          optional.push({ ...product, subtotal });
        }
      });

      function card(p) {
        return `
          <div class="result-card">
	   <img src="${p.image}" alt="${p.name}" style="width: 100%; border-radius: 8px; margin-bottom: 10px;" />
            <h3>${p.name}</h3>
            <p>建議用量：${p.recommendedQty} 盒／月</p>
            <p>單價：$${p.price}｜總金額：$${p.subtotal}</p>
          </div>
        `;
      }

      priorityList.innerHTML = priority.map(card).join("");
      optionalList.innerHTML = optional.map(card).join("");
      priorityTotal.textContent = `$${priorityTotalVal}`;
      allTotal.textContent = `$${total}`;
      progressBar.style.width = `100%`;
    }

    nextBtn.addEventListener("click", () => {
      if (!collectAnswers()) return;
      current++;
      if (current < quizData.length) {
        renderQuestion();
        if (current === quizData.length - 1) {
          nextBtn.classList.add("hidden");
          submitBtn.classList.remove("hidden");
        }
      }
    });

    submitBtn.addEventListener("click", () => {
      if (!collectAnswers()) return;
      renderResults();
    });
    renderQuestion();
      function restartQuiz() {
        window.location.href = "https://freyja-cin.github.io/iho/";
  }
    
  </script>
</body>
</html>
