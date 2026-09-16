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
      background: #0f172a;
      min-height: 100vh;
      margin: 0;
      padding: 30px 20px;
      color: #0f172a;
      overflow-x: hidden;
      position: relative;
    }

    /* Ambient Liquid Background Blobs */
    .bg-blobs {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: -1;
      overflow: hidden;
      pointer-events: none;
    }

    .blob {
      position: absolute;
      border-radius: 50%;
      filter: blur(90px);
      opacity: 0.65;
      animation: float 14s infinite alternate ease-in-out;
    }

    .blob-1 {
      top: -10%;
      left: -10%;
      width: 550px;
      height: 550px;
      background: linear-gradient(135deg, #3b82f6, #8b5cf6);
    }

    .blob-2 {
      bottom: -15%;
      right: -10%;
      width: 650px;
      height: 650px;
      background: linear-gradient(135deg, #06b6d4, #3b82f6);
      animation-delay: -5s;
    }

    .blob-3 {
      top: 40%;
      left: 35%;
      width: 450px;
      height: 450px;
      background: linear-gradient(135deg, #ec4899, #8b5cf6);
      animation-delay: -9s;
    }

    @keyframes float {
      0% {
        transform: translate(0, 0) scale(1) rotate(0deg);
      }
      50% {
        transform: translate(60px, 80px) scale(1.1) rotate(180deg);
      }
      100% {
        transform: translate(-40px, 40px) scale(0.95) rotate(360deg);
      }
    }

    .container {
      max-width: 1000px;
      margin: 0 auto;
      position: relative;
      z-index: 1;
    }

    /* Glass Container Helper */
    .glass-panel {
      background: rgba(255, 255, 255, 0.65);
      backdrop-filter: blur(20px) saturate(180%);
      -webkit-backdrop-filter: blur(20px) saturate(180%);
      border: 1px solid rgba(255, 255, 255, 0.8);
      box-shadow: 0 16px 40px 0 rgba(31, 38, 135, 0.12),
                  inset 0 1px 0 0 rgba(255, 255, 255, 0.9);
      border-radius: 24px;
    }

    header {
      text-align: center;
      margin-bottom: 30px;
      padding: 25px;
    }

    header h1 {
      color: #0f172a;
      margin-top: 0;
      margin-bottom: 10px;
      font-size: 32px;
      letter-spacing: -0.5px;
      text-shadow: 0 2px 10px rgba(255, 255, 255, 0.5);
    }

    header p {
      color: #334155;
      margin: 6px 0;
      font-weight: 500;
    }

    .credit-badge {
      display: inline-block;
      margin-top: 10px;
      background: rgba(99, 102, 241, 0.15);
      color: #3730a3;
      border: 1px solid rgba(99, 102, 241, 0.3);
      backdrop-filter: blur(8px);
      font-weight: bold;
      font-size: 13px;
      padding: 6px 16px;
      border-radius: 30px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    }

    /* Search & Top Action Bar */
    .action-bar {
      margin-bottom: 20px;
    }

    .search-box {
      display: flex;
      gap: 12px;
    }

    input[type="text"] {
      flex: 1;
      padding: 16px 22px;
      background: rgba(255, 255, 255, 0.7);
      border: 1.5px solid rgba(255, 255, 255, 0.9);
      border-radius: 16px;
      font-size: 16px;
      color: #0f172a;
      outline: none;
      backdrop-filter: blur(10px);
      box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.02),
                  0 8px 20px rgba(0, 0, 0, 0.04);
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    }

    input[type="text"]::placeholder {
      color: #64748b;
    }

    input[type="text"]:focus {
      background: rgba(255, 255, 255, 0.9);
      border-color: #6366f1;
      box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.25),
                  0 10px 25px rgba(0, 0, 0, 0.08);
      transform: translateY(-1px);
    }

    .btn {
      padding: 16px 28px;
      font-size: 16px;
      font-weight: bold;
      color: white;
      border: none;
      border-radius: 16px;
      cursor: pointer;
      position: relative;
      overflow: hidden;
      transition: all 0.3s ease;
    }

    .btn-primary {
      background: linear-gradient(135deg, #4f46e5 0%, #3b82f6 100%);
      box-shadow: 0 8px 20px rgba(79, 70, 229, 0.3),
                  inset 0 1px 0 rgba(255, 255, 255, 0.4);
    }

    .btn-primary:hover {
      transform: translateY(-2px);
      box-shadow: 0 12px 25px rgba(79, 70, 229, 0.4),
                  inset 0 1px 0 rgba(255, 255, 255, 0.6);
    }

    .btn:active {
      transform: translateY(1px);
    }

    /* Glass Registered rooms tags */
    .room-tags {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      align-items: center;
      padding: 12px 20px;
      margin-bottom: 25px;
    }

    .room-tag {
      background: rgba(255, 255, 255, 0.5);
      color: #1e1b4b;
      padding: 6px 14px;
      border-radius: 12px;
      font-size: 14px;
      font-weight: 600;
      cursor: pointer;
      border: 1px solid rgba(255, 255, 255, 0.8);
      backdrop-filter: blur(8px);
      transition: all 0.25s ease;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.03);
    }

    .room-tag:hover {
      background: rgba(99, 102, 241, 0.2);
      color: #4338ca;
      border-color: rgba(99, 102, 241, 0.4);
      transform: translateY(-2px) scale(1.03);
      box-shadow: 0 6px 15px rgba(99, 102, 241, 0.15);
    }

    /* Status & Alerts */
    .status-msg {
      text-align: center;
      font-size: 16px;
      margin: 15px 0;
      color: #ef4444;
      font-weight: bold;
      text-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
    }

    /* Room Details Card */
    .room-card {
      padding: 30px;
      margin-bottom: 35px;
      display: none;
      scroll-margin-top: 20px;
      animation: glassFadeIn 0.5s cubic-bezier(0.16, 1, 0.3, 1);
    }

    @keyframes glassFadeIn {
      from {
        opacity: 0;
        transform: translateY(20px) scale(0.98);
      }
      to {
        opacity: 1;
        transform: translateY(0) scale(1);
      }
    }

    .room-header {
      border-bottom: 1px solid rgba(226, 232, 240, 0.8);
      padding-bottom: 18px;
      margin-bottom: 22px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
    }

    .room-title {
      font-size: 28px;
      color: #0f172a;
      margin: 0;
      font-weight: 800;
    }

    .room-badge {
      background: rgba(59, 130, 246, 0.12);
      color: #2563eb;
      border: 1px solid rgba(59, 130, 246, 0.25);
      padding: 6px 16px;
      border-radius: 20px;
      font-weight: bold;
      font-size: 14px;
    }

    /* Hero Image */
    .room-main-img-container {
      width: 100%;
      height: 400px;
      border-radius: 20px;
      overflow: hidden;
      margin-bottom: 25px;
      box-shadow: 0 12px 30px rgba(0, 0, 0, 0.12);
      border: 1px solid rgba(255, 255, 255, 0.6);
      position: relative;
    }

    .room-main-img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
      transition: transform 0.5s ease;
    }

    .room-main-img-container:hover .room-main-img {
      transform: scale(1.02);
    }

    .room-desc {
      background: rgba(255, 255, 255, 0.5);
      border-left: 4px solid #6366f1;
      padding: 18px 20px;
      border-radius: 0 16px 16px 0;
      font-size: 16px;
      line-height: 1.6;
      margin-top: 8px;
      margin-bottom: 30px;
      color: #334155;
    }

    /* Steps Grid */
    .steps-title {
      font-size: 20px;
      font-weight: 700;
      color: #0f172a;
      margin-bottom: 18px;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .steps-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(170px, 1fr));
      gap: 16px;
    }

    .step-card {
      background: rgba(255, 255, 255, 0.5);
      border: 1px solid rgba(255, 255, 255, 0.8);
      border-radius: 16px;
      overflow: hidden;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.03);
      display: flex;
      flex-direction: column;
      backdrop-filter: blur(10px);
      transition: all 0.3s ease;
    }

    .step-card:hover {
      transform: translateY(-4px);
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.08);
      background: rgba(255, 255, 255, 0.75);
    }

    .step-badge {
      background: linear-gradient(135deg, #1e3a8a, #3b82f6);
      color: white;
      font-size: 12px;
      font-weight: bold;
      padding: 6px;
      text-align: center;
      letter-spacing: 0.5px;
    }

    .step-card img {
      width: 100%;
      height: 130px;
      object-fit: cover;
      background-color: #cbd5e1;
    }

    .step-card .step-text {
      padding: 12px;
      font-size: 13px;
      color: #334155;
      line-height: 1.4;
      flex: 1;
      font-weight: 500;
    }

    /* All Rooms Section */
    .all-rooms-section {
      margin-top: 40px;
      padding-top: 30px;
    }

    .section-title {
      font-size: 22px;
      color: #0f172a;
      margin-bottom: 22px;
      display: flex;
      align-items: center;
      gap: 10px;
      font-weight: 800;
      text-shadow: 0 2px 10px rgba(255, 255, 255, 0.5);
    }

    .all-rooms-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 20px;
    }

    .room-item-card {
      padding: 22px;
      cursor: pointer;
      transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }

    .room-item-card:hover {
      transform: translateY(-6px) scale(1.02);
      box-shadow: 0 20px 40px rgba(31, 38, 135, 0.18),
                  0 0 20px rgba(99, 102, 241, 0.2);
      border-color: rgba(99, 102, 241, 0.5);
    }

    .room-item-header {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      margin-bottom: 10px;
    }

    .room-item-id {
      font-size: 22px;
      font-weight: 800;
      color: #0f172a;
    }

    .room-item-name {
      font-weight: 700;
      color: #1e293b;
      font-size: 16px;
      margin-bottom: 8px;
    }

    .room-item-desc {
      font-size: 13px;
      color: #475569;
      margin-bottom: 18px;
      line-height: 1.5;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
    }

    /* Footer */
    footer {
      text-align: center;
      margin-top: 50px;
      padding: 25px 0;
      color: #f8fafc;
      font-size: 14px;
      text-shadow: 0 2px 4px rgba(0,0,0,0.3);
    }
  </style>
</head>
<body>

  <!-- Animated Liquid Background -->
  <div class="bg-blobs">
    <div class="blob blob-1"></div>
    <div class="blob blob-2"></div>
    <div class="blob blob-3"></div>
  </div>

  <div class="container">
    <header class="glass-panel">
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

    <div class="room-tags glass-panel">
      <small style="color:#334155; font-weight:bold;">ค้นหารวดเร็ว:</small>
      <div id="roomTagList" style="display:flex; gap:8px; flex-wrap:wrap;"></div>
    </div>

    <div id="statusMsg" class="status-msg"></div>

    <div id="roomCard" class="room-card glass-panel">
      <div class="room-header">
        <div>
          <h2 id="roomTitle" class="room-title"></h2>
        </div>
        <span id="roomLocation" class="room-badge"></span>
      </div>

      <!-- รูปภาพใหญ่ของห้องเรียน เหนือรายละเอียด -->
      <div class="room-main-img-container">
        <img id="roomMainImg" class="room-main-img" src="https://i.postimg.cc/Bn9kCGjs/IMG20260913154210-(1).jpg" alt="รูปห้องเรียน" onerror="this.src='https://via.placeholder.com/800x400?text=PCSHSCR+Room'">
      </div>

      <div>
        <strong style="color: #0f172a; font-size: 16px;">📋 รายละเอียดเกี่ยวกับห้อง:</strong>
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
        card.className = 'room-item-card glass-panel';
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
          <button class="btn btn-primary" style="width: 100%; padding: 10px 14px; font-size: 14px; margin-top: 10px; border-radius: 12px;">
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
