<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ระบบค้นหาและนำทางห้องเรียน (School Room Finder)</title>
  <style>
    * {
      box-sizing: border-box;
      font-family: 'Sukhumvit Set', 'Prompt', 'Segoe UI', Tahoma, sans-serif;
    }

    body {
      background-color: #f1f5f9;
      min-height: 100vh;
      margin: 0;
      padding: 30px 20px;
      color: #1e293b;
      position: relative;
      overflow-x: hidden;
    }

    /* Soft Liquid Ambient Background */
    .liquid-bg {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: -1;
      overflow: hidden;
    }

    .liquid-blob {
      position: absolute;
      border-radius: 50%;
      filter: blur(100px);
      opacity: 0.45;
      animation: floatSmooth 18s ease-in-out infinite alternate;
    }

    .blob-1 {
      top: -10%;
      right: 10%;
      width: 450px;
      height: 450px;
      background: #c7d2fe;
    }

    .blob-2 {
      bottom: -10%;
      left: 5%;
      width: 500px;
      height: 500px;
      background: #bae6fd;
      animation-delay: -6s;
    }

    .blob-3 {
      top: 40%;
      right: 30%;
      width: 350px;
      height: 350px;
      background: #e0e7ff;
      animation-delay: -12s;
    }

    @keyframes floatSmooth {
      0% { transform: translate(0, 0) scale(1); }
      50% { transform: translate(30px, 40px) scale(1.05); }
      100% { transform: translate(-30px, 20px) scale(0.95); }
    }

    .container {
      max-width: 960px;
      margin: 0 auto;
      position: relative;
      z-index: 1;
    }

    /* Subtle Glassmorphism Card */
    .glass-card {
      background: rgba(255, 255, 255, 0.75);
      backdrop-filter: blur(16px);
      -webkit-backdrop-filter: blur(16px);
      border: 1px solid rgba(255, 255, 255, 0.8);
      border-radius: 20px;
      box-shadow: 0 10px 30px -5px rgba(0, 0, 0, 0.05),
                  0 2px 6px -1px rgba(0, 0, 0, 0.02);
    }

    header {
      text-align: center;
      margin-bottom: 25px;
      padding: 24px;
    }

    header h1 {
      color: #1e3a8a;
      margin-top: 0;
      margin-bottom: 8px;
      font-size: 28px;
    }

    header p {
      color: #64748b;
      margin: 4px 0;
      font-size: 15px;
    }

    .credit-badge {
      display: inline-block;
      margin-top: 10px;
      background: rgba(224, 231, 255, 0.7);
      color: #3730a3;
      font-weight: 600;
      font-size: 13px;
      padding: 4px 14px;
      border-radius: 20px;
      border: 1px solid rgba(199, 210, 254, 0.5);
    }

    /* Search & Top Action Bar */
    .action-bar {
      margin-bottom: 18px;
    }

    .search-box {
      display: flex;
      gap: 10px;
    }

    input[type="text"] {
      flex: 1;
      padding: 14px 18px;
      background: rgba(255, 255, 255, 0.85);
      border: 1px solid rgba(203, 213, 225, 0.8);
      border-radius: 14px;
      font-size: 15px;
      color: #1e293b;
      outline: none;
      backdrop-filter: blur(8px);
      box-shadow: 0 2px 4px rgba(0,0,0,0.02);
      transition: all 0.2s ease;
    }

    input[type="text"]:focus {
      background: #ffffff;
      border-color: #3b82f6;
      box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
    }

    .btn {
      padding: 14px 24px;
      font-size: 15px;
      font-weight: 600;
      color: white;
      border: none;
      border-radius: 14px;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    .btn-primary {
      background: #2563eb;
      box-shadow: 0 4px 12px rgba(37, 99, 235, 0.2);
    }

    .btn-primary:hover {
      background: #1d4ed8;
      box-shadow: 0 6px 16px rgba(37, 99, 235, 0.3);
      transform: translateY(-1px);
    }

    .btn:active {
      transform: translateY(0);
    }

    /* Quick Tags */
    .room-tags {
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      align-items: center;
      padding: 12px 18px;
      margin-bottom: 25px;
    }

    .room-tag {
      background: rgba(255, 255, 255, 0.6);
      color: #334155;
      padding: 5px 12px;
      border-radius: 10px;
      font-size: 13px;
      font-weight: 500;
      cursor: pointer;
      border: 1px solid rgba(226, 232, 240, 0.8);
      transition: all 0.2s ease;
    }

    .room-tag:hover {
      background: #ffffff;
      color: #2563eb;
      border-color: #93c5fd;
      transform: translateY(-1px);
    }

    /* Status & Alerts */
    .status-msg {
      text-align: center;
      font-size: 15px;
      margin: 15px 0;
      color: #dc2626;
      font-weight: 600;
    }

    /* Room Details Card */
    .room-card {
      padding: 25px;
      margin-bottom: 30px;
      display: none;
      scroll-margin-top: 20px;
      animation: fadeIn 0.4s ease-out;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }

    .room-header {
      border-bottom: 1px solid #e2e8f0;
      padding-bottom: 15px;
      margin-bottom: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
    }

    .room-title {
      font-size: 24px;
      color: #0f172a;
      margin: 0;
    }

    .room-badge {
      background: #eff6ff;
      color: #2563eb;
      padding: 5px 12px;
      border-radius: 16px;
      font-weight: 600;
      font-size: 13px;
      border: 1px solid #dbeafe;
    }

    .room-main-img-container {
      width: 100%;
      height: 380px;
      border-radius: 14px;
      overflow: hidden;
      margin-bottom: 20px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.05);
      background-color: #e2e8f0;
    }

    .room-main-img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
    }

    .room-desc {
      background: rgba(248, 250, 252, 0.8);
      border-left: 3.5px solid #2563eb;
      padding: 14px 16px;
      border-radius: 0 10px 10px 0;
      font-size: 15px;
      line-height: 1.6;
      margin-top: 6px;
      margin-bottom: 25px;
      color: #334155;
    }

    .steps-title {
      font-size: 18px;
      font-weight: 700;
      color: #1e293b;
      margin-bottom: 15px;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .steps-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
      gap: 14px;
    }

    .step-card {
      background: rgba(255, 255, 255, 0.7);
      border: 1px solid rgba(226, 232, 240, 0.9);
      border-radius: 12px;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      transition: transform 0.2s ease;
    }

    .step-card:hover {
      transform: translateY(-2px);
      background: #ffffff;
    }

    .step-badge {
      background: #1e3a8a;
      color: white;
      font-size: 11px;
      font-weight: 600;
      padding: 4px;
      text-align: center;
    }

    .step-card img {
      width: 100%;
      height: 120px;
      object-fit: cover;
      background-color: #cbd5e1;
    }

    .step-card .step-text {
      padding: 10px;
      font-size: 12px;
      color: #475569;
      line-height: 1.4;
      flex: 1;
    }

    /* All Rooms Section */
    .all-rooms-section {
      margin-top: 35px;
      padding-top: 20px;
    }

    .section-title {
      font-size: 20px;
      color: #1e3a8a;
      margin-bottom: 18px;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .all-rooms-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
      gap: 16px;
    }

    .room-item-card {
      padding: 20px;
      cursor: pointer;
      transition: all 0.25s ease;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }

    .room-item-card:hover {
      transform: translateY(-3px);
      box-shadow: 0 12px 24px -6px rgba(0, 0, 0, 0.08);
      border-color: #93c5fd;
      background: rgba(255, 255, 255, 0.9);
    }

    .room-item-header {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      margin-bottom: 8px;
    }

    .room-item-id {
      font-size: 20px;
      font-weight: 700;
      color: #1e293b;
    }

    .room-item-name {
      font-weight: 600;
      color: #334155;
      font-size: 15px;
      margin-bottom: 6px;
    }

    .room-item-desc {
      font-size: 13px;
      color: #64748b;
      margin-bottom: 14px;
      line-height: 1.5;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
    }

    /* Footer */
    footer {
      text-align: center;
      margin-top: 40px;
      padding: 20px 0;
      color: #64748b;
      font-size: 13px;
    }
  </style>
</head>
<body>

  <!-- Ambient Fluid Accent Background -->
  <div class="liquid-bg">
    <div class="liquid-blob blob-1"></div>
    <div class="liquid-blob blob-2"></div>
    <div class="liquid-blob blob-3"></div>
  </div>

  <div class="container">
    <header class="glass-card">
      <h1>📍 ค้นหาและนำทางห้องเรียน PCSHSCR</h1>
      <p>พิมพ์เลขห้อง หรือเลือกห้องด้านล่างเพื่อดูรายละเอียดและเส้นทางเดินไปห้อง</p>
      <p>CHIANG RAI District</p>
      <span class="credit-badge">Made by PCSHSCR student M.4/3</span>
    </header>

    <div class="action-bar">
      <div class="search-box">
        <input type="text" id="searchInput" placeholder="กรอกเลขห้องหรือชื่อห้อง (เช่น 321, 101, ห้องคอม)...">
        <button class="btn btn-primary" onclick="searchRoom()">ค้นหา</button>
      </div>
    </div>

    <div class="room-tags glass-card">
      <small style="color:#64748b; font-weight:bold;">ค้นหารวดเร็ว:</small>
      <div id="roomTagList" style="display:flex; gap:6px; flex-wrap:wrap;"></div>
    </div>

    <div id="statusMsg" class="status-msg"></div>

    <div id="roomCard" class="room-card glass-card">
      <div class="room-header">
        <div>
          <h2 id="roomTitle" class="room-title"></h2>
        </div>
        <span id="roomLocation" class="room-badge"></span>
      </div>

      <div class="room-main-img-container">
        <img id="roomMainImg" class="room-main-img" src="https://i.postimg.cc/Bn9kCGjs/IMG20260913154210-(1).jpg" alt="รูปห้องเรียน" onerror="this.src='https://via.placeholder.com/800x400?text=PCSHSCR+Room'">
      </div>

      <div>
        <strong style="color:#1e293b; font-size: 15px;"> รายละเอียดเกี่ยวกับห้อง:</strong>
        <div id="roomDesc" class="room-desc"></div>
      </div>

      <div class="steps-title">
         รูปภาพเส้นทางนำทางไปห้อง (5 ขั้นตอน):
      </div>

      <div id="stepsGrid" class="steps-grid"></div>
    </div>

    <div class="all-rooms-section">
      <h2 class="section-title"> รายการห้องทั้งหมดในระบบ (คลิกเพื่อดูเส้นทาง)</h2>
      <div id="allRoomsGrid" class="all-rooms-grid"></div>
    </div>

    <footer>
      <p>© PCSHSCR Room Navigation System | Made by PCSHSCR student M.4/3</p>
    </footer>
  </div>

  <script>
    const initialRooms = [
      {
        id: "321",
        location: "อาคาร 3 ชั้น 2",
        name: "ห้องปฏิบัติการคอมพิวเตอร์ 1",
        description: "ห้องปฏิบัติการคอมพิวเตอร์สำหรับการเรียนการสอนวิชาออกแบบและเทคโนโลยี และโปรแกรมมิ่ง มีเครื่องคอมพิวเตอร์ 40 เครื่อง พร้อมระบบปรับอากาศและเครื่องโปรเจกเตอร์",
        mainImage: "https://i.postimg.cc/Bn9kCGjs/IMG20260913154210-(1).jpg",
        images: [
          { url: "https://i.postimg.cc/Bn9kCGjs/IMG20260913154210-(1).jpg", desc: "1. เริ่มต้นจากซุ้มประตูหน้าโรงเรียน เดินตรงเข้าสู่ลานกิจกรรมกลาง" },
          { url: "https://picsum.photos/seed/step2_321/600/400", desc: "2. เดินตรงไปทางอาคาร 3 (สังเกตป้ายสีฟ้าหน้าอาคาร)" },
          { url: "https://picsum.photos/seed/step3_321/600/400", desc: "3. ขึ้นบันไดกลางไปยังชั้น 2" },
          { url: "https://picsum.photos/seed/step4_321/600/400", desc: "4. เลี้ยวซ้ายตามทางเดิน เดินผ่านห้องพักครูหมวดวิทยาศาสตร์" },
          { url: "https://picsum.photos/seed/step5_321/600/400", desc: "5. ถึงห้อง 321 อยู่ทางขวามือ ประตูกระจกขอบขาว" }
        ]
      },
      {
        id: "101",
        location: "อาคาร 1 ชั้น 1",
        name: "ห้องแนะแนวและห้องพยาบาล",
        description: "ห้องสำหรับการปรึกษาด้านการเรียน สุขภาพจิต และเป็นจุดปฐมพยาบาลเบื้องต้น มีเตียงพักฟื้น 4 เตียงและยาพื้นฐานครบครัน",
        mainImage: "https://picsum.photos/seed/step5_101/600/400",
        images: [
          { url: "https://picsum.photos/seed/step1_101/600/400", desc: "1. เริ่มจากจุดประชาสัมพันธ์หน้าอาคาร 1" },
          { url: "https://picsum.photos/seed/step2_101/600/400", desc: "2. เดินตรงเข้าโถงอาคาร 1" },
          { url: "https://picsum.photos/seed/step3_101/600/400", desc: "3. เลี้ยวขวาตรงป้ายจุดบริการนักเรียน" },
          { url: "https://picsum.photos/seed/step4_101/600/400", desc: "4. เดินตามระเบียงทางเดินริมสวน" },
          { url: "https://picsum.photos/seed/step5_101/600/400", desc: "5. ถึงห้อง 101 ประตูกระจกบานเลื่อนสีเขียว" }
        ]
      },
      {
        id: "หอประชุมแคแสด",
        location: "หอประชุมแคแสด",
        name: "หอประชุมแคแสด",
        description: "ห้องประชุมขนาดใหญ่รองรับนักเรียนจุฬาภรณ์ราชวิทยาลัยเชียงราย ใช้ในการจัดงาน และกิจกรรมภายในโรงเรียน",
        mainImage: "https://ibb.co/YF5pcZh3",
        images: [
          { url: "https://ibb.co/YBwhdmTZ", desc: "1. เริ่มจากหน้าตึกอำนวยการ" },
          { url: "https://ibb.co/zTWhFHqm", desc: "2. เดินผ่านริมน้ำ" },
          { url: "https://ibb.co/q3Z58bbq", desc: "3. เลี้ยวซ้ายบริเวณ 3 แยกริมน้ำ" },
          { url: "https://i.ibb.co/Zpf600VW/IMG20260913152354.jpg", desc: "4. เดินผ่านสะพาน" },
          { url: "https://i.ibb.co/7t91251J/IMG20260913152556-1.jpg", desc: "5. ถึงหอประชุมแคแสด จุดหมายอยู่บริเวณด้านหน้า" }
        ]
      }
    ];

    function getRoomsData() {
      return initialRooms;
    }

    function selectRoom(id) {
      document.getElementById('searchInput').value = id;
      searchRoom();
      document.getElementById('roomCard').scrollIntoView({ behavior: 'smooth', block: 'start' });
    }

    function searchRoom() {
      const query = document.getElementById('searchInput').value.trim().toLowerCase();
      const statusMsg = document.getElementById('statusMsg');
      const roomCard = document.getElementById('roomCard');

      if (!query) {
        statusMsg.textContent = 'กรุณากรอกเลขห้องหรือเลือกห้องจากรายการด้านล่าง';
        roomCard.style.display = 'none';
        return;
      }

      const rooms = getRoomsData();
      const found = rooms.find(r => 
        r.id.toLowerCase().includes(query) || 
        r.name.toLowerCase().includes(query) ||
        r.location.toLowerCase().includes(query)
      );

      if (!found) {
        statusMsg.textContent = `❌ ไม่พบข้อมูลห้อง "${query}" ในระบบ กรุณาตรวจสอบเลขห้องอีกครั้ง`;
        roomCard.style.display = 'none';
        return;
      }

      statusMsg.textContent = '';
      document.getElementById('roomTitle').textContent = `ห้อง ${found.id} - ${found.name}`;
      document.getElementById('roomLocation').textContent = found.location;
      document.getElementById('roomDesc').textContent = found.description;
      
      const mainImgUrl = found.mainImage || found.images[found.images.length - 1]?.url || found.images[0]?.url;
      document.getElementById('roomMainImg').src = mainImgUrl;

      const stepsGrid = document.getElementById('stepsGrid');
      stepsGrid.innerHTML = '';

      found.images.forEach((imgObj, index) => {
        const stepCard = document.createElement('div');
        stepCard.className = 'step-card';
        stepCard.innerHTML = `
          <div class="step-badge">ขั้นตอนที่ ${index + 1}</div>
          <img src="${imgObj.url}" alt="Step ${index + 1}" onerror="this.src='https://via.placeholder.com/600x400?text=Step+${index+1}'">
          <div class="step-text">${imgObj.desc}</div>
        `;
        stepsGrid.appendChild(stepCard);
      });

      roomCard.style.display = 'block';
    }

    function renderRoomTags() {
      const tagList = document.getElementById('roomTagList');
      const rooms = getRoomsData();
      tagList.innerHTML = '';
      rooms.forEach(r => {
        const tag = document.createElement('span');
        tag.className = 'room-tag';
        tag.textContent = `ห้อง ${r.id}`;
        tag.onclick = () => selectRoom(r.id);
        tagList.appendChild(tag);
      });
    }

    function renderAllRoomCards() {
      const grid = document.getElementById('allRoomsGrid');
      const rooms = getRoomsData();
      grid.innerHTML = '';

      rooms.forEach(r => {
        const card = document.createElement('div');
        card.className = 'room-item-card glass-card';
        card.onclick = () => selectRoom(r.id);
        card.innerHTML = `
          <div>
            <div class="room-item-header">
              <div class="room-item-id">ห้อง ${r.id}</div>
              <span class="room-badge">${r.location}</span>
            </div>
            <div class="room-item-name">${r.name}</div>
            <div class="room-item-desc">${r.description}</div>
          </div>
          <button class="btn btn-primary" style="width: 100%; padding: 8px 12px; font-size: 13px; margin-top: 8px; border-radius: 10px;">
            📍 ดูเส้นทางไปห้องนี้
          </button>
        `;
        grid.appendChild(card);
      });
    }

    document.getElementById('searchInput').addEventListener('keypress', function (e) {
      if (e.key === 'Enter') {
        searchRoom();
      }
    });

    renderRoomTags();
    renderAllRoomCards();
  </script>
</body>
</html>
