made by pcshscr student m.4/3
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
      margin: 0;
    }

    /* Search & Top Action Bar */
    .action-bar {
      display: flex;
      gap: 12px;
      margin-bottom: 25px;
      flex-wrap: wrap;
    }

    .search-box {
      flex: 1;
      display: flex;
      gap: 8px;
      min-width: 280px;
    }

    input[type="text"], textarea, select {
      padding: 12px 16px;
      border: 2px solid #cbd5e1;
      border-radius: 8px;
      font-size: 16px;
      outline: none;
      transition: border-color 0.2s;
    }

    input[type="text"]:focus, textarea:focus {
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

    .btn-success {
      background-color: #059669;
    }

    .btn-success:hover {
      background-color: #047857;
    }

    .btn-secondary {
      background-color: #64748b;
    }

    .btn-secondary:hover {
      background-color: #475569;
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

    /* Modal Form for Adding Room */
    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.5);
      justify-content: center;
      align-items: center;
      z-index: 1000;
      padding: 15px;
    }

    .modal-content {
      background: white;
      width: 100%;
      max-width: 650px;
      max-height: 90vh;
      overflow-y: auto;
      border-radius: 12px;
      padding: 25px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.2);
    }

    .modal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
    }

    .modal-header h2 {
      margin: 0;
      color: #1e3a8a;
    }

    .close-btn {
      background: none;
      border: none;
      font-size: 24px;
      cursor: pointer;
      color: #64748b;
    }

    .form-group {
      margin-bottom: 15px;
    }

    .form-group label {
      display: block;
      font-weight: bold;
      margin-bottom: 6px;
      color: #334155;
    }

    .form-group input, .form-group textarea {
      width: 100%;
    }

    .form-row {
      display: flex;
      gap: 10px;
    }

    .form-row .form-group {
      flex: 1;
    }

    .image-inputs {
      background-color: #f8fafc;
      padding: 15px;
      border-radius: 8px;
      border: 1px dashed #cbd5e1;
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
  </style>
</head>
<body>

  <div class="container">
    <header>
      <h1>📍 ค้นหาและนำทางห้องเรียน</h1>
      <p>พิมพ์เลขห้องเพื่อดูข้อมูลรายละเอียดและเส้นทางเดินไปห้อง (5 ขั้นตอน)</p>
    </header>

    <div class="action-bar">
      <div class="search-box">
        <input type="text" id="searchInput" placeholder="กรอกเลขห้องหรือชื่อห้อง (เช่น 321, 101, ห้องคอม)...">
        <button class="btn btn-primary" onclick="searchRoom()">ค้นหา</button>
      </div>
      <button class="btn btn-success" onclick="openAddModal()">+ เพิ่มห้องใหม่</button>
    </div>

    <div class="room-tags">
      <small style="color:#64748b; font-weight:bold;">ห้องที่มีในระบบ:</small>
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

      <div>
        <strong>📋 รายละเอียดเกี่ยวกับห้อง:</strong>
        <div id="roomDesc" class="room-desc"></div>
      </div>

      <div class="steps-title">
        🗺️ รูปภาพเส้นทางนำทางไปห้อง (5 ขั้นตอน):
      </div>

      <div id="stepsGrid" class="steps-grid"></div>
    </div>
  </div>

  <div id="addModal" class="modal">
    <div class="modal-content">
      <div class="modal-header">
        <h2>➕ เพิ่มข้อมูลห้องเรียนใหม่</h2>
        <button class="close-btn" onclick="closeAddModal()">&times;</button>
      </div>

      <form id="addRoomForm" onsubmit="saveNewRoom(event)">
        <div class="form-row">
          <div class="form-group">
            <label>เลขห้อง / รหัสห้อง *</label>
            <input type="text" id="newRoomId" placeholder="เช่น 405" required>
          </div>
          <div class="form-group">
            <label>ชื่อห้อง / ตำแหน่งชั้น *</label>
            <input type="text" id="newRoomName" placeholder="เช่น อาคาร 4 ชั้น 3" required>
          </div>
        </div>

        <div class="form-group">
          <label>รายละเอียดเกี่ยวกับห้อง *</label>
          <textarea id="newRoomDesc" rows="3" placeholder="เช่น เป็นห้องปฏิบัติการเคมี มีอุปกรณ์ทดลองครบครัน..." required></textarea>
        </div>

        <div class="form-group">
          <label>📷 URL รูปภาพทางไปห้อง (5 รูปภาพ) *</label>
          <div class="image-inputs">
            <small style="color:#64748b; display:block; margin-bottom:10px;">กรอก URL รูปภาพ หรือปล่อยว่างไว้เพื่อใช้รูปตัวอย่างนำทาง</small>
            
            <div style="display:flex; flex-direction:column; gap:8px;">
              <input type="text" id="img1" placeholder="รูปที่ 1: จุดเริ่มต้น (เช่น หน้าเสาธง/อาคาร)">
              <input type="text" id="img2" placeholder="รูปที่ 2: ทางเลี้ยว / บันได">
              <input type="text" id="img3" placeholder="รูปที่ 3: ระหว่างทางเดิน / ชั้น">
              <input type="text" id="img4" placeholder="รูปที่ 4: หน้าโถง / ทางเดินก่อนถึง">
              <input type="text" id="img5" placeholder="รูปที่ 5: ถึงประตูหน้าห้องเป้าหมาย">
            </div>
          </div>
        </div>

        <div style="display: flex; justify-content: flex-end; gap: 10px; margin-top: 20px;">
          <button type="button" class="btn btn-secondary" onclick="closeAddModal()">ยกเลิก</button>
          <button type="submit" class="btn btn-success">บันทึกข้อมูลห้อง</button>
        </div>
      </form>
    </div>
  </div>

  <script>
    // 1. ข้อมูลห้องเริ่มต้น (Mock Data)
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

    // โหลดข้อมูลจาก LocalStorage หรือใช้ข้อมูลเริ่มต้น
    function getRoomsData() {
      const stored = localStorage.getItem('school_rooms_data');
      if (stored) {
        return JSON.parse(stored);
      }
      localStorage.setItem('school_rooms_data', JSON.stringify(initialRooms));
      return initialRooms;
    }

    // บันทึกข้อมูลห้องกลับลง LocalStorage
    function saveRoomsData(data) {
      localStorage.setItem('school_rooms_data', JSON.stringify(data));
      renderRoomTags();
    }

    // ค้นหาห้อง
    function searchRoom() {
      const query = document.getElementById('searchInput').value.trim().toLowerCase();
      const statusMsg = document.getElementById('statusMsg');
      const roomCard = document.getElementById('roomCard');

      if (!query) {
        statusMsg.textContent = 'กรุณากรอกเลขห้องหรือชื่อห้องที่ต้องการค้นหา';
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
        statusMsg.textContent = `❌ ไม่พบข้อมูลห้อง "${query}" ในระบบ กรุณาตรวจสอบเลขห้องหรือกดปุ่ม +เพิ่มห้องใหม่`;
        roomCard.style.display = 'none';
        return;
      }

      // แสดงผลเมื่อพบห้อง
      statusMsg.textContent = '';
      document.getElementById('roomTitle').textContent = `ห้อง ${found.id} - ${found.name}`;
      document.getElementById('roomLocation').textContent = found.location;
      document.getElementById('roomDesc').textContent = found.description;

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
        tag.onclick = () => {
          document.getElementById('searchInput').value = r.id;
          searchRoom();
        };
        tagList.appendChild(tag);
      });
    }

    // เปิด/ปิด Modal เพิ่มห้อง
    function openAddModal() {
      document.getElementById('addModal').style.display = 'flex';
    }

    function closeAddModal() {
      document.getElementById('addModal').style.display = 'none';
      document.getElementById('addRoomForm').reset();
    }

    // บันทึกห้องใหม่
    function saveNewRoom(e) {
      e.preventDefault();
      
      const id = document.getElementById('newRoomId').value.trim();
      const location = document.getElementById('newRoomName').value.trim();
      const description = document.getElementById('newRoomDesc').value.trim();

      const imgInputs = [
        document.getElementById('img1').value.trim(),
        document.getElementById('img2').value.trim(),
        document.getElementById('img3').value.trim(),
        document.getElementById('img4').value.trim(),
        document.getElementById('img5').value.trim()
      ];

      const stepNames = [
        "1. จุดเริ่มต้นทางเดิน",
        "2. จุดสังเกตแรก/จุดเลี้ยว",
        "3. ระหว่างทางเดิน/โถงบันได",
        "4. บริเวณใกล้เคียงห้อง",
        "5. หน้าประตูห้องเป้าหมาย"
      ];

      const images = imgInputs.map((url, i) => ({
        url: url || `https://picsum.photos/seed/newroom_${id}_step${i+1}/600/400`,
        desc: `${stepNames[i]}`
      }));

      const newRoom = {
        id,
        location,
        name: `ห้องเรียน ${id}`,
        description,
        images
      };

      const rooms = getRoomsData();
      // ถ้ามีห้องนี้แล้วให้เขียนทับ
      const existingIdx = rooms.findIndex(r => r.id === id);
      if (existingIdx >= 0) {
        rooms[existingIdx] = newRoom;
      } else {
        rooms.push(newRoom);
      }

      saveRoomsData(rooms);
      closeAddModal();

      // ค้นหาห้องที่เพิ่งเพิ่มทันที
      document.getElementById('searchInput').value = id;
      searchRoom();
    }

    // รองรับกดปุ่ม Enter ในช่องค้นหา
    document.getElementById('searchInput').addEventListener('keypress', function (e) {
      if (e.key === 'Enter') {
        searchRoom();
      }
    });

    // เริ่มต้นแสดงผล Tag เมื่อเปิดหน้าเว็บ
    renderRoomTags();
  </script>
</body>
</html>
