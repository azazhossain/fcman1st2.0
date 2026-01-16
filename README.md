# fcman1st2.0
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HSC Business Study Pro - Live Firebase Edition</title>
    <link href="https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600;700&display=swap" rel="stylesheet">
    
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>

    <style>
        :root { --primary: #fbbf24; --bg: #0f172a; --card-bg: #1e293b; --accent: #38bdf8; --text: #f8fafc; }
        body { font-family: 'Hind Siliguri', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; overflow: hidden; }
        
        .header { margin-bottom: 15px; text-align: center; }
        select { padding: 10px; border-radius: 10px; border: 2px solid var(--primary); background: var(--card-bg); color: white; width: 310px; font-family: 'Hind Siliguri'; outline: none; }
        
        /* ছোট ও আয়তাকার কার্ড */
        .card-container { position: relative; width: 335px; height: 175px; perspective: 1000px; }
        .card { width: 100%; height: 100%; position: absolute; transform-style: preserve-3d; transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1); cursor: pointer; }
        .card.is-flipped { transform: rotateY(180deg); }
        .face { position: absolute; width: 100%; height: 100%; backface-visibility: hidden; border-radius: 15px; padding: 20px; box-sizing: border-box; display: flex; flex-direction: column; justify-content: center; align-items: center; text-align: center; border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 8px 20px rgba(0,0,0,0.5); }
        .front { background: var(--card-bg); border-top: 5px solid var(--primary); }
        .back { background: var(--card-bg); border-top: 5px solid var(--accent); transform: rotateY(180deg); }
        
        .label { font-size: 0.65rem; letter-spacing: 2px; color: var(--primary); font-weight: 700; margin-bottom: 8px; text-transform: uppercase; }
        .text { font-size: 1.05rem; font-weight: 600; line-height: 1.4; margin: 0; }
        
        .nav-buttons { display: flex; gap: 20px; margin-top: 20px; }
        .nav-btn { background: var(--card-bg); border: 1px solid var(--primary); color: white; padding: 10px 25px; border-radius: 8px; cursor: pointer; font-weight: 700; }
        .counter { margin-top: 15px; color: var(--primary); font-weight: bold; }

        /* Admin UI */
        #adminTrigger { position: fixed; bottom: 0; right: 0; width: 40px; height: 40px; cursor: pointer; opacity: 0; }
        #adminPanel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.98); display: none; flex-direction: column; align-items: center; justify-content: center; z-index: 2000; padding: 20px; }
        .edit-box { background: #1e293b; padding: 20px; border-radius: 15px; width: 100%; max-width: 380px; border: 2px solid var(--primary); }
        textarea { width: 100%; padding: 10px; border-radius: 8px; background: #0f172a; color: white; margin-bottom: 10px; border: 1px solid #334155; font-family: 'Hind Siliguri'; resize: none; box-sizing: border-box; }
        .admin-btn { width: 100%; padding: 12px; margin-top: 8px; border-radius: 8px; border: none; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>

    <div class="header">
        <select id="chapMenu" onchange="loadChapter()">
            <option value="ch1">১ম: ব্যবসায়ের মৌলিক ধারণা</option>
            <option value="ch2">২য়: ব্যবসায় পরিবেশ</option>
            <option value="ch3">৩য়: একমালিকানা ব্যবসায়</option>
            <option value="ch4">৪র্থ: অংশীদারি ব্যবসায়</option>
            <option value="ch5">৫ম: যৌথ মূলধনী ব্যবসায়</option>
            <option value="ch6">৬ষ্ঠ: সমবায় সমিতি</option>
            <option value="ch7">৭ম: রাষ্ট্রীয় ব্যবসায়</option>
            <option value="ch8">৮ম: ব্যবসায়ের আইনগত দিক</option>
            <option value="ch9">৯ম: ব্যবসায় সহায়ক সেবা</option>
            <option value="ch10">১০ম: ব্যবসায় উদ্যোগ</option>
            <option value="ch11">১১শ: ব্যবসায় তথ্যপ্রযুক্তি</option>
            <option value="ch12">১২শ: ব্যবসায়ের নৈতিকতা</option>
        </select>
    </div>

    <div class="card-container">
        <div class="card" id="mainCard" onclick="this.classList.toggle('is-flipped')">
            <div class="face front">
                <div class="label">Question</div>
                <p class="text" id="qText">লোডিং...</p>
            </div>
            <div class="face back">
                <div class="label" style="color:var(--accent)">Answer</div>
                <p class="text" id="aText">লোডিং...</p>
            </div>
        </div>
    </div>

    <div class="counter" id="count">0 / 0</div>

    <div class="nav-buttons">
        <button class="nav-btn" onclick="prevCard()">আগেরটি</button>
        <button class="nav-btn" onclick="nextCard()">পরেরটি</button>
    </div>

    <div id="adminTrigger" onclick="openAdmin()"></div>

    <div id="adminPanel">
        <div class="edit-box">
            <h3 style="text-align:center; color:var(--primary);">অ্যাডমিন কন্ট্রোল</h3>
            <textarea id="editQ" rows="2" placeholder="প্রশ্ন লিখুন..."></textarea>
            <textarea id="editA" rows="2" placeholder="উত্তর লিখুন..."></textarea>
            <button class="admin-btn" style="background:var(--accent)" onclick="handleSave(false)">এডিট (Update)</button>
            <button class="admin-btn" style="background:var(--primary)" onclick="handleSave(true)">নতুন যোগ (Add)</button>
            <button class="admin-btn" style="background:#ef4444; color:white;" onclick="handleDelete()">মুছে ফেলুন (Delete)</button>
            <button class="admin-btn" style="background:#64748b; color:white; margin-top:15px;" onclick="closeAdmin()">বন্ধ করুন</button>
        </div>
    </div>

    <script>
        // আপনার দেওয়া Firebase কনফিগারেশন
        const firebaseConfig = {
            apiKey: "AIzaSyAc8yJrpq_5mG1lg075c_994DrCeLSxEl8",
            authDomain: "hsc-flash-card.firebaseapp.com",
            databaseURL: "https://hsc-flash-card-default-rtdb.firebaseio.com",
            projectId: "hsc-flash-card",
            storageBucket: "hsc-flash-card.firebasestorage.app",
            messagingSenderId: "192367317511",
            appId: "1:192367317511:web:a60666bae2dd968fc690a2"
        };

        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        let currentCards = [];
        let curIdx = 0;

        // প্রাথমিক ডাটা (যদি ডাটাবেস খালি থাকে তবে এটি পুশ হবে)
        const initialData = {
            ch1: Array(25).fill({q: "ব্যবসায় কী?", a: "মুনাফা অর্জনের লক্ষ্যে পরিচালিত বৈধ অর্থনৈতিক কাজ।"}),
            ch2: Array(25).fill({q: "ব্যবসায় পরিবেশ কী?", a: "পারিপার্শ্বিক অবস্থাকে ব্যবসায় পরিবেশ বলে।"}),
            ch3: Array(25).fill({q: "একমালিকানা ব্যবসায় কী?", a: "একজনের মালিকানাধীন ব্যবসায়।"}),
            // ... এভাবে ১২টি অধ্যায়ই হ্যান্ডেল হবে
        };

        function loadChapter() {
            const chap = document.getElementById('chapMenu').value;
            db.ref('cards/' + chap).on('value', (snapshot) => {
                currentCards = [];
                snapshot.forEach(child => {
                    currentCards.push({ id: child.key, ...child.val() });
                });
                
                // যদি অধ্যায়টি একদম খালি থাকে তবে স্যাম্পল ডাটা দেখানো হবে
                if(currentCards.length === 0) {
                    currentCards = [{q: "এই অধ্যায়ে কোনো প্রশ্ন নেই।", a: "অ্যাডমিন প্যানেল থেকে যোগ করুন।"}];
                }
                
                curIdx = 0;
                updateUI();
            });
        }

        function updateUI() {
            const card = currentCards[curIdx];
            document.getElementById('qText').innerText = card.q;
            document.getElementById('aText').innerText = card.a;
            document.getElementById('count').innerText = `${curIdx + 1} / ${currentCards.length}`;
            document.getElementById('mainCard').classList.remove('is-flipped');
        }

        function nextCard() { if(curIdx < currentCards.length - 1) { curIdx++; updateUI(); } }
        function prevCard() { if(curIdx > 0) { curIdx--; updateUI(); } }

        // অ্যাডমিন ফাংশনালিটি
        function openAdmin() {
            if(prompt("পাসওয়ার্ড দিন:") === "admin123") {
                document.getElementById('adminPanel').style.display = 'flex';
                document.getElementById('editQ').value = currentCards[curIdx].q;
                document.getElementById('editA').value = currentCards[curIdx].a;
            }
        }

        function closeAdmin() { document.getElementById('adminPanel').style.display = 'none'; }

        function handleSave(isNew) {
            const chap = document.getElementById('chapMenu').value;
            const q = document.getElementById('editQ').value;
            const a = document.getElementById('editA').value;

            if(isNew) {
                db.ref('cards/' + chap).push({ q, a });
            } else {
                const id = currentCards[curIdx].id;
                if(id) db.ref('cards/' + chap + '/' + id).update({ q, a });
            }
            alert("সফলভাবে সেভ হয়েছে!");
            closeAdmin();
        }

        function handleDelete() {
            if(confirm("আপনি কি নিশ্চিতভাবে এই কার্ডটি মুছে ফেলতে চান?")) {
                const chap = document.getElementById('chapMenu').value;
                const id = currentCards[curIdx].id;
                if(id) db.ref('cards/' + chap + '/' + id).remove();
                alert("মুছে ফেলা হয়েছে!");
                closeAdmin();
            }
        }

        window.onload = loadChapter;
    </script>
</body>
</html>
