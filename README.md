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
      background-color: #f0f4f8;
      margin: 0;
      padding: 20px;
      color: #333;
    }

    .container {
      max-width: 1000px;
      margin: 0 auto;
    }

    header {
      text-align: center;
      margin-bottom: 30px;
    }

    header h1 {
      color: #1e3a8a;
      margin-bottom: 8px;
    }

    header p {
      color: #64748b;
      margin: 4px 0;
    }

    .credit-badge {
      display: inline-block;
      margin-top: 6px;
      background-color: #e0e7ff;
      color: #1e3a8a;
      font-weight: bold;
      font-size: 13px;
      padding: 4px 12px;
      border-radius: 20px;
    }

    /* Search & Top Action Bar */
    .action-bar {
      display: flex;
      gap: 12px;
      margin-bottom: 25px;
    }

    .search-box {
      flex: 1;
      display: flex;
      gap: 8px;
    }

    input[type="text"] {
      padding: 12px 16px;
      border: 2px solid #cbd5e1;
      border-radius: 8px;
      font-size: 16px;
      outline: none;
      transition: border-color 0.2s;
    }

    input[type="text"]:focus {
      border-color: #2563eb;
    }

    .search-box input {
      flex: 1;
    }

    .btn {
      padding: 12px 20px;
      font-size: 16px;
      font-weight: bold;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.2s, transform 0.1s;
    }

    .btn:active {
      transform: scale(0.98);
    }

    .btn-primary {
      background-color: #2563eb;
    }

    .btn-primary:hover {
      background-color: #1d4ed8;
    }

    /* Status & Alerts */
    .status-msg {
      text-align: center;
      font-size: 16px;
      margin: 15px 0;
      color: #dc2626;
      font-weight: bold;
    }

    /* Room Details Card */
    .room-card {
      background: white;
      border-radius: 12px;
      padding: 25px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
      margin-bottom: 30px;
      display: none;
      scroll-margin-top: 20px;
    }

    .room-header {
      border-bottom: 2px solid #e2e8f0;
      padding-bottom: 15px;
      margin-bottom: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
    }

    .room-title {
      font-size: 28px;
      color: #1e293b;
      margin: 0;
    }

    .room-badge {
      background-color: #eff6ff;
      color: #2563eb;
      padding: 6px 14px;
      border-radius: 20px;
      font-weight: bold;
      font-size: 14px;
    }

    /* Large Hero Image for Room */
    .room-main-img-container {
      width: 100%;
      height: 380px;
      border-radius: 12px;
      overflow: hidden;
      margin-bottom: 20px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
      background-color: #e2e8f0;
    }

    .room-main-img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
    }

    .room-desc {
      background-color: #f8fafc;
      border-left: 4px solid #2563eb;
      padding: 15px;
      border-radius: 0 8px 8px 0;
      font-size: 16px;
      line-height: 1.6;
      margin-bottom: 25px;
    }

    /* 5 Navigation Steps (Image Gallery) */
    .steps-title {
      font-size: 20px;
      color: #1e293b;
      margin-bottom: 15px;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .steps-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(170px, 1fr));
      gap: 15px;
    }

    .step-card {
      background: #ffffff;
      border: 1px solid #e2e8f0;
      border-radius: 10px;
      overflow: hidden;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.04);
      display: flex;
      flex-direction: column;
    }

    .step-badge {
      background: #1e3a8a;
      color: white;
      font-size: 12px;
      font-weight: bold;
      padding: 4px 8px;
      text-align: center;
    }

    .step-card img {
      width: 100%;
      height: 130px;
      object-fit: cover;
      background-color: #e2e8f0;
    }

    .step-card .step-text {
      padding: 10px;
      font-size: 13px;
      color: #475569;
      line-height: 1.4;
      flex: 1;
    }

    /* Registered rooms list tags */
    .room-tags {
      margin-top: 15px;
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      align-items: center;
    }

    .room-tag {
      background: #e0e7ff;
      color: #3730a3;
      padding: 4px 10px;
      border-radius: 6px;
      font-size: 13px;
      cursor: pointer;
      transition: background 0.2s;
    }

    .room-tag:hover {
      background: #c7d2fe;
    }

    /* All Rooms Section */
    .all-rooms-section {
      margin-top: 40px;
      border-top: 2px solid #e2e8f0;
      padding-top: 25px;
    }

    .section-title {
      font-size: 22px;
      color: #1e3a8a;
      margin-bottom: 20px;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .all-rooms-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 18px;
    }

    .room-item-card {
      background: white;
      border-radius: 10px;
      padding: 20px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.05);
      border: 2px solid #e2e8f0;
      cursor: pointer;
      transition: transform 0.2s, box-shadow 0.2s, border-color 0.2s;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }

    .room-item-card:hover {
      transform: translateY(-4px);
      box-shadow: 0 8px 20px rgba(37, 99, 235, 0.12);
      border-color: #2563eb;
    }

    .room-item-header {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      margin-bottom: 8px;
    }

    .room-item-id {
      font-size: 22px;
      font-weight: bold;
      color: #1e293b;
    }

    .room-item-name {
      font-weight: bold;
      color: #334155;
      font-size: 16px;
      margin-bottom: 8px;
    }

    .room-item-desc {
      font-size: 13px;
      color: #64748b;
      margin-bottom: 15px;
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
      font-size: 14px;
      border-top: 1px solid #e2e8f0;
    }
  </style>
</head>
<body>

  <div class="container">
    <header>
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

    <div class="room-tags">
      <small style="color:#64748b; font-weight:bold;">ค้นหารวดเร็ว:</small>
      <div id="roomTagList"></div>
    </div>

    <div id="statusMsg" class="status-msg"></div>

    <div id="roomCard" class="room-card">
      <div class="room-header">
        <div>
          <h2 id="roomTitle" class="room-title"></h2>
          <span id="roomLocation" class="room-badge"></span>
        </div>
      </div>

      <!-- รูปภาพใหญ่ของห้องเรียน เหนือรายละเอียด -->
      <div class="room-main-img-container">
        <img id="roomMainImg" class="room-main-img" src="" alt="รูปห้องเรียน" onerror="this.src='https://via.placeholder.com/800x400?text=PCSHSCR+Room'">
      </div>

      <div>
        <strong>📋 รายละเอียดเกี่ยวกับห้อง:</strong>
        <div id="roomDesc" class="room-desc"></div>
      </div>

      <div class="steps-title">
        🗺️ รูปภาพเส้นทางนำทางไปห้อง (5 ขั้นตอน):
      </div>

      <div id="stepsGrid" class="steps-grid"></div>
    </div>

    <!-- โซนรายการห้องทั้งหมดด้านล่าง -->
    <div class="all-rooms-section">
      <h2 class="section-title">🏢 รายการห้องทั้งหมดในระบบ (คลิกเพื่อดูเส้นทาง)</h2>
      <div id="allRoomsGrid" class="all-rooms-grid"></div>
    </div>

    <footer>
      <p>© PCSHSCR Room Navigation System | Made by PCSHSCR student M.4/3</p>
    </footer>
  </div>

  <script>
    // ข้อมูลห้องเริ่มต้นในระบบ
    const initialRooms = [
      {
        id: "321",
        location: "อาคาร 3 ชั้น 2",
        name: "ห้องปฏิบัติการคอมพิวเตอร์ 1",
        description: "ห้องปฏิบัติการคอมพิวเตอร์สำหรับการเรียนการสอนวิชาออกแบบและเทคโนโลยี และโปรแกรมมิ่ง มีเครื่องคอมพิวเตอร์ 40 เครื่อง พร้อมระบบปรับอากาศและเครื่องโปรเจกเตอร์",
        images: [
          { url: "https://picsum.photos/seed/step1_321/600/400", desc: "1. เริ่มต้นจากซุ้มประตูหน้าโรงเรียน เดินตรงเข้าสู่ลานกิจกรรมกลาง" },
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
        images: [
          { url: "https://picsum.photos/seed/step1_101/600/400", desc: "1. เริ่มจากจุดประชาสัมพันธ์หน้าอาคาร 1" },
          { url: "https://picsum.photos/seed/step2_101/600/400", desc: "2. เดินตรงเข้าโถงอาคาร 1" },
          { url: "https://picsum.photos/seed/step3_101/600/400", desc: "3. เลี้ยวขวาตรงป้ายจุดบริการนักเรียน" },
          { url: "https://picsum.photos/seed/step4_101/600/400", desc: "4. เดินตามระเบียงทางเดินริมสวน" },
          { url: "https://picsum.photos/seed/step5_101/600/400", desc: "5. ถึงห้อง 101 ประตูกระจกบานเลื่อนสีเขียว" }
        ]
      }
    ];

    function getRoomsData() {
      return initialRooms;
    }

    // เลือกห้องจากการคลิกการ์ดห้อง
    function selectRoom(id) {
      document.getElementById('searchInput').value = id;
      searchRoom();
      document.getElementById('roomCard').scrollIntoView({ behavior: 'smooth', block: 'start' });
    }

    // ค้นหาห้อง
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

      // แสดงผลเมื่อพบห้อง
      statusMsg.textContent = '';
      document.getElementById('roomTitle').textContent = `ห้อง ${found.id} - ${found.name}`;
      document.getElementById('roomLocation').textContent = found.location;
      document.getElementById('roomDesc').textContent = found.description;
      
      // ตั้งค่ารูปภาพใหญ่ (ดึงรูปขั้นตอนสุดท้าย ซึ่งเป็นรูปหน้าห้องเป้าหมายมาโชว์)
      const mainImgUrl = found.images[found.images.length - 1]?.url || found.images[0]?.url;
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

    // สร้าง Tag ปุ่มกดค้นหาด่วน
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

    // สร้างการ์ดห้องทั้งหมดแสดงด้านล่าง
    function renderAllRoomCards() {
      const grid = document.getElementById('allRoomsGrid');
      const rooms = getRoomsData();
      grid.innerHTML = '';

      rooms.forEach(r => {
        const card = document.createElement('div');
        card.className = 'room-item-card';
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
          <button class="btn btn-primary" style="width: 100%; padding: 8px 12px; font-size: 14px; margin-top: 10px;">
            📍 ดูเส้นทางไปห้องนี้
          </button>
        `;
        grid.appendChild(card);
      });
    }

    // รองรับกดปุ่ม Enter ในช่องค้นหา
    document.getElementById('searchInput').addEventListener('keypress', function (e) {
      if (e.key === 'Enter') {
        searchRoom();
      }
    });

    // เริ่มต้นแสดงผลระบบเมื่อเปิดหน้าเว็บ
    renderRoomTags();
    renderAllRoomCards();
  </script>
</body>
</html>
