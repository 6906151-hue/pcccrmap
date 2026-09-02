made by pcshscr
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ค้นหาห้องเรียนสำหรับ PCSHSCR</title>
  <style>
    * {
      box-sizing: border-box;
      font-family: 'Sukhumvit Set', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background-color: #f4f7f6;
      margin: 0;
      padding: 20px;
      display: flex;
      justify-content: center;
    }

    .container {
      width: 100%;
      max-width: 900px;
      background: #ffffff;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
    }

    h1 {
      text-align: center;
      color: #2c3e50;
      margin-bottom: 25px;
    }

    .search-box {
      display: flex;
      gap: 10px;
      margin-bottom: 25px;
    }

    input[type="text"] {
      flex: 1;
      padding: 12px 18px;
      font-size: 16px;
      border: 2px solid #ddd;
      border-radius: 8px;
      outline: none;
      transition: border-color 0.3s;
    }

    input[type="text"]:focus {
      border-color: #3498db;
    }

    button {
      padding: 12px 24px;
      font-size: 16px;
      background-color: #3498db;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.3s;
    }

    button:hover {
      background-color: #2980b9;
    }

    .status {
      text-align: center;
      font-size: 16px;
      color: #e74c3c;
      margin: 15px 0;
      font-weight: bold;
    }

    .status.loading {
      color: #3498db;
    }

    .result-section {
      display: none;
    }

    .word-header {
      border-bottom: 2px solid #eee;
      padding-bottom: 10px;
      margin-bottom: 20px;
    }

    .word-title {
      font-size: 32px;
      color: #2c3e50;
      margin: 0;
    }

    .phonetics {
      font-size: 18px;
      color: #7f8c8d;
      margin-top: 5px;
    }

    .meaning-item {
      margin-bottom: 15px;
      line-height: 1.6;
      background-color: #fafafa;
      padding: 12px 15px;
      border-left: 4px solid #3498db;
      border-radius: 0 6px 6px 0;
    }

    .part-of-speech {
      font-weight: bold;
      color: #e67e22;
      text-transform: capitalize;
    }

    .images-header {
      margin-top: 30px;
      font-size: 20px;
      color: #2c3e50;
    }

    .image-gallery {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
      gap: 12px;
      margin-top: 15px;
    }

    .image-gallery img {
      width: 100%;
      height: 140px;
      object-fit: cover;
      border-radius: 8px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
      background-color: #eee;
    }
  </style>
</head>
<body>

  <div class="container">
    <h1>ค้นหาคำศัพท์ & รูปภาพประกอบ</h1>
    
    <div class="search-box">
      <input type="text" id="searchInput" placeholder="พิมพ์คำศัพท์ภาษาอังกฤษ (เช่น cat, car, guitar)...">
      <button onclick="searchWord()">ค้นหา</button>
    </div>

    <div id="status" class="status"></div>

    <div id="resultSection" class="result-section">
      <div class="word-header">
        <h2 id="wordTitle" class="word-title"></h2>
        <div id="phonetics" class="phonetics"></div>
      </div>

      <div id="meanings"></div>

      <h3 class="images-header">รูปภาพประกอบ (5 รูป):</h3>
      <div id="imageGallery" class="image-gallery"></div>
    </div>
  </div>

  <script>
    async function searchWord() {
      const input = document.getElementById('searchInput').value.trim();
      const statusDiv = document.getElementById('status');
      const resultSection = document.getElementById('resultSection');
      const meaningsDiv = document.getElementById('meanings');
      const imageGallery = document.getElementById('imageGallery');

      if (!input) {
        statusDiv.className = 'status';
        statusDiv.textContent = 'กรุณากรอกคำศัพท์ที่ต้องการค้นหา';
        return;
      }

      statusDiv.className = 'status loading';
      statusDiv.textContent = 'กำลังค้นหาข้อมูลและรูปภาพ...';
      resultSection.style.display = 'none';
      meaningsDiv.innerHTML = '';
      imageGallery.innerHTML = '';

      try {
        // ดึงข้อมูลคำแปลและรูปภาพขนานกัน
        const [dictRes, images] = await Promise.all([
          fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${encodeURIComponent(input)}`),
          fetchImages(input)
        ]);

        if (!dictRes.ok) {
          throw new Error('ไม่พบคำศัพท์นี้ในพจนานุกรม กรุณาตรวจสอบคำศัพท์อีกครั้ง');
        }

        const dictData = await dictRes.json();
        const wordInfo = dictData[0];

        // แสดงผลคำศัพท์
        document.getElementById('wordTitle').textContent = wordInfo.word;
        document.getElementById('phonetics').textContent = wordInfo.phonetic || (wordInfo.phonetics.find(p => p.text)?.text || '');

        // แสดงผลคำแปล
        wordInfo.meanings.forEach(meaning => {
          const item = document.createElement('div');
          item.className = 'meaning-item';
          
          const pos = document.createElement('span');
          pos.className = 'part-of-speech';
          pos.textContent = `[${meaning.partOfSpeech}] `;
          
          const defText = meaning.definitions[0]?.definition || '';
          const def = document.createTextNode(defText);
          
          item.appendChild(pos);
          item.appendChild(def);
          meaningsDiv.appendChild(item);
        });

        // แสดงผลรูปภาพ 5 รูป
        images.forEach((url, idx) => {
          const img = document.createElement('img');
          img.src = url;
          img.alt = `${input} ${idx + 1}`;
          img.onerror = function() {
            // หากรูปโหลดไม่สำเร็จ ใช้รูปภาพสำรอง
            this.src = `https://loremflickr.com/400/400/${encodeURIComponent(input)}?lock=${idx + 10}`;
          };
          imageGallery.appendChild(img);
        });

        statusDiv.textContent = '';
        resultSection.style.display = 'block';

      } catch (error) {
        statusDiv.className = 'status';
        statusDiv.textContent = error.message;
      }
    }

    // ดึงรูปภาพจาก Wikimedia Commons API (หรือใช้ LoremFlickr เป็นสำรอง)
    async function fetchImages(keyword) {
      try {
        const wikiUrl = `https://commons.wikimedia.org/w/api.php?action=query&generator=search&gsrnamespace=6&gsrsearch=${encodeURIComponent(keyword)}&gsrlimit=10&prop=pageimages&piprop=thumbnail&pithumbsize=400&format=json&origin=*`;
        const res = await fetch(wikiUrl);
        const data = await res.json();

        if (data.query && data.query.pages) {
          const fetchedUrls = Object.values(data.query.pages)
            .filter(page => page.thumbnail && page.thumbnail.source)
            .map(page => page.thumbnail.source)
            .slice(0, 5);

          if (fetchedUrls.length >= 5) {
            return fetchedUrls;
          }
          
          // เติมรูปให้ครบ 5 รูปกรณีได้ไม่ครบจาก Wikimedia
          while (fetchedUrls.length < 5) {
            fetchedUrls.push(`https://loremflickr.com/400/400/${encodeURIComponent(keyword)}?lock=${fetchedUrls.length + 1}`);
          }
          return fetchedUrls;
        }
      } catch (e) {
        console.warn("Wikimedia API fetch failed, falling back to LoremFlickr.");
      }

      // กรณี API ขัดข้อง ใช้บริการ LoremFlickr สำรอง 5 รูป
      const fallbackUrls = [];
      for (let i = 1; i <= 5; i++) {
        fallbackUrls.push(`https://loremflickr.com/400/400/${encodeURIComponent(keyword)}?lock=${i}`);
      }
      return fallbackUrls;
    }

    // รองรับการกด Enter ในช่องค้นหา
    document.getElementById('searchInput').addEventListener('keypress', function (e) {
      if (e.key === 'Enter') {
        searchWord();
      }
    });
  </script>
</body>
</html>
