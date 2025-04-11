#GIAOHOA
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QR Member Checker</title>
  <script src="https://unpkg.com/html5-qrcode" type="text/javascript"></script>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      text-align: center;
      padding: 20px;
      max-width: 500px;
      margin: 0 auto;
      background-color: #f5f5f5;
    }
    #scanner-container {
      background: white;
      border-radius: 12px;
      padding: 20px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }
    #reader {
      width: 100%;
      max-width: 300px;
      margin: 0 auto;
      border: 2px solid #ddd;
      border-radius: 8px;
      overflow: hidden;
    }
    #result {
      font-size: 24px;
      font-weight: bold;
      margin: 20px 0;
      padding: 20px;
      border-radius: 8px;
      min-height: 80px;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.3s ease;
    }
    .valid {
      color: #2e7d32;
      background-color: #e8f5e9;
      border: 2px solid #a5d6a7;
    }
    .invalid {
      color: #c62828;
      background-color: #ffebee;
      border: 2px solid #ef9a9a;
    }
    .initial {
      color: #555;
      background-color: #e3f2fd;
      border: 2px solid #bbdefb;
    }
    .error {
      color: #ff6f00;
      background-color: #fff3e0;
      border: 2px solid #ffe0b2;
    }
    button {
      padding: 12px 24px;
      margin: 10px 5px;
      background: #1976d2;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 16px;
      transition: background 0.3s;
      min-width: 150px;
    }
    button:hover {
      background: #1565c0;
    }
    button:disabled {
      background: #b0bec5;
      cursor: not-allowed;
    }
    #stopButton {
      background: #d32f2f;
    }
    #stopButton:hover {
      background: #b71c1c;
    }
    .status {
      margin-top: 15px;
      font-size: 14px;
      color: #666;
    }
    @media (max-width: 480px) {
      body {
        padding: 10px;
      }
      #result {
        font-size: 20px;
        padding: 15px;
      }
      button {
        padding: 10px 15px;
        min-width: 120px;
      }
    }
  </style>
</head>
<body>
  <div id="scanner-container">
    <h1>KIỂM TRA THÀNH VIÊN</h1>
    <div id="reader"></div>
    <div id="result" class="initial">Chờ khởi tạo...</div>
    <div class="status" id="statusText"></div>
    <button id="stopButton" disabled>DỪNG QUÉT</button>
    <button id="retryButton" style="display:none;">THỬ LẠI</button>
  </div>

  <script>
    const MEMBER_LIST = new Set(
      [
      "Nguyễn Hoàng Vĩnh Thịnh",
      "Bùi Hạnh Phương",
      "Lê Vũ Ngọc Hân",
      "Lê Nguyễn Thành Trung",
      "Nguyễn Hoàng Bảo Anh",
      "Nguyễn Bảo Thy",
      "Nguyễn Thanh Tâm Huyền",
      "Lưu Gia Đạt",
      "Nguyễn Ngọc Thanh Nhã",
      "Trần Gia Linh",
      "Nguyễn Hoàng Trung Tài",
      "Nguyễn Ngọc Ái Tâm",
      "Trần Nhật Khánh Tâm",
      "Nguyễn Mạnh Châu Anh",
      "Nguyễn Cửu Đăng Hoàng",
      "Trần Võ Nhật Quyền",
      "Nguyễn Văn Gia Phúc",
      "Nguyễn Ngọc Khánh Băng",
      "Nguyễn Lê Phương Toàn",
      "Tưởng Phạm Thu Hà",
      "Vũ Ngọc Thùy Hương",
      "Phạm Ngọc Linh Đan",
      "Lê Trung Nghĩa",
      "Nguyễn Lê Nam Phương",
      "Lư Hữu Khánh",
      "Trần Thị Bảo Ngọc",
      "Trần Bửu Nghi",
      "Nguyễn Đỗ Hoàng Nam",
      "Phạm Minh Triết",
      "Đào Viết Trí",
      "Vương Hoàng Diệp",
      "Ngô Quốc Hưng",
      "Nguyễn Quốc Cường",
      "Trì Nguyễn Minh Quân",
      "Tô Tuấn Huy",
      "Nguyễn Ngọc Thiên Thanh",
      "Nguyễn Nhật Anh Kim",
      "Trần Đình Gia Đạt",
      "Đinh Thị Chúc An",
      "Lê Phương Hoàng Mai",
      "Lê Bảo Nhi",
      "Nguyễn Dũng Hoàng Khiêm",
      "Tô Hoàng Xuân Mai",
      "Loại Văn Bá Vương",
      "Phạm Lê Gia Như",
      "Nguyễn Bảo Huy",
      "Trịnh Trung An",
      "Hồ Gia Huy",
      "Bùi Minh Thiện",
      "Đỗ Ngọc Hoàng",
      "Nguyễn Minh Khang",
      "Nguyễn Hùng Cường",
      "Hồ Đăng Khoa",
      "Võ Hoàng Gia Phát",
      "Trương Thiện Nhân",
      "Trương Công Nhật",
      "Hồ Nhật Nam",
      "Cao Thục Uyên",
      "Bùi Nguyễn Mai Anh",
      "Phạm Trương Tấn Khải",
      "Phạm Gia Anh",
      "Nguyễn Vũ Trâm Anh",
      "Nguyễn Vân Anh",
      "Huỳnh Tiết Lâm Quỳnh",
      "Hoàng Gia Bảo",
      "Võ Quốc An",
      "Lê Anh Minh",
      "Trương Bảo Trân",
      "Hà Đặng Bảo Trân",
      "Nguyễn Hoàng Anh Thư",
      "Nguyễn Ngọc Hương",
      "Nguyễn Chí Thiện",
      "Lương Bảo Ngân",
      "Nguyễn Trần Minh Thư",
      "Phan Võ Đức Thành",
      "Lê Hoàng Mai Khôi",
      "Nguyễn Ngọc Trân",
      "Lê Anh Thư",
      "Trương Gia Bình",
      "Hoàng Kim Bảo Ân",
      "Bùi Ngọc Thiên Kim",
      "Nguyễn Võ Thanh Tuyền",
      "Nguyễn Gia Nghi",
      "Đỗ Nghiêm Đức Huy",
      "Trương Trúc Linh",
      "Nguyễn Phương Vy",
      "Vũ Lương Phương Nghi",
      "Trương Thị Phương Anh",
      "Hoàng Song Thịnh",
      "Hồng Bảo Hân",
      "Trần Ngọc Diễm My",
      "Lê Nguyễn Hoài Anh",
      "Đỗ Ngọc Trân",
      "Hồ Khánh Linh",
      "Hồ Viết Minh Thông",
      "Trương Đình Bảo Thy",
      "Nguyễn Ngọc Gia Linh",
      "Trương Vũ Như Quỳnh",
      "Lê Hoàng Mai",
      "Trần Vĩnh Khánh Vy",
      "Lê Anh Khoa",
      "Lê Minh Hân",
      "Trương Trí Trung",
      "Đoàn Thụy Minh An",
      "Huỳnh Đăng Khoa",
      "Phạm Nguyễn Quỳnh Thy",
      "Nguyễn Đặng Tường Vy",
      "Nguyễn Công Bảo Phúc",
      "Viên Bảo Phúc",
      "Nguyễn Hoàng Nhân",
      "Lê Trúc Phương",
      "Phạm Đoàn Duyên Hải",
      "Đoàn Ngọc Bích",
      "Trần Hoài Nam",
      "Nguyễn Minh Huy",
      "Nguyễn Vũ Hải Đình",
      "Trần Tuấn Nam",
      "Trần Phạm Hoàng Trí",
      "Phạm Anh Thư",
      "Nguyễn Thùy Ý Nhi",
      "Võ Trần Thanh Vân",
      "Lê Trần Thùy An",
      "Nguyễn Ngô Thảo Mi",
      "Trần Dương Phương Ngọc",
      "Trần Phương Anh",
      "Hứa Cát Linh",
      "Lê Minh Bảo",
      "Nguyễn Phạm Quỳnh Anh",
      "Nguyễn Hoàng Phương Nguyên",
      "Huỳnh Tấn Phước",
      "Nguyễn Anh Tuấn",
      "Lê Vũ Hà Anh",
      "Đỗ Ngọc Phương Linh",
      "Đào Gia Khang",
      "Võ Hồng Hảo",
      "Nguyễn Đặng Kathy Thanh Ngọc",
      "Đỗ Tùng Quân",
      "Trần Thanh Hạ",
      "Mai Lê Quang Long",
      "Nguyễn Nguyên Đăng"
      ].map(name => name.trim().toLowerCase())
    );

    let html5QrCode;
    let isScanning = false;
    let currentCameraId = null;

    function initializeScanner() {
      html5QrCode = new Html5Qrcode("reader");
      updateStatus("Đang tải scanner...");
    }

    function logEvent(eventName, eventParams = {}) {
      // Có thể thêm analytics khác ở đây nếu cần
      console.log(`Event: ${eventName}`, eventParams);
    }

    async function startScanning(cameraId) {
      try {
        updateStatus("Đang khởi động camera...");
        
        await html5QrCode.start(
          cameraId,
          {
            fps: 7,
            qrbox: { width: 250, height: 250 },
            aspectRatio: 1.0
          },
          handleScanResult,
          handleScanError
        );
        
        isScanning = true;
        currentCameraId = cameraId;
        document.getElementById('stopButton').disabled = false;
        document.getElementById('retryButton').style.display = 'none';
        document.getElementById('result').className = "initial";
        document.getElementById('result').textContent = "Đang quét...";
        updateStatus("Sẵn sàng quét mã");
        logEvent('scan_started');
        
      } catch (error) {
        console.error("Scanner error:", error);
        showError(`Lỗi camera: ${error.message}`);
        logEvent('scan_error', { error: error.message });
      }
    }

    async function stopScanning() {
      if (!isScanning) return;
      
      try {
        updateStatus("Đang dừng scanner...");
        await html5QrCode.stop();
        isScanning = false;
        document.getElementById('stopButton').disabled = true;
        document.getElementById('retryButton').style.display = 'inline-block';
        document.getElementById('result').textContent = "Đã dừng";
        updateStatus("Scanner đã dừng");
        logEvent('scan_stopped');
      } catch (error) {
        console.error("Stop error:", error);
        updateStatus("Lỗi khi dừng scanner");
      }
    }

    function handleScanResult(decodedText) {
      const normalizedText = decodedText.trim().toLowerCase();
      const resultElement = document.getElementById('result');
      
      if (MEMBER_LIST.has(normalizedText)) {
        resultElement.textContent = "TRUE";
        resultElement.className = "valid";
        logEvent('valid_scan', { member: decodedText.trim() });
        
        setTimeout(() => stopScanning(), 1500);
      } else {
        resultElement.textContent = "FALSE";
        resultElement.className = "invalid";
        logEvent('invalid_scan', { code: decodedText.trim() });
      }
      
      updateStatus(`Đã quét: ${decodedText.trim()}`);
    }

    function handleScanError(error) {
      console.warn("Scan error:", error);
      updateStatus("Lỗi khi quét mã");
    }

    function updateStatus(text) {
      document.getElementById('statusText').textContent = text;
    }

    function showError(message) {
      const resultElement = document.getElementById('result');
      resultElement.textContent = message;
      resultElement.className = "error";
      document.getElementById('retryButton').style.display = 'inline-block';
      updateStatus("Đã xảy ra lỗi");
    }

    document.getElementById('stopButton').addEventListener('click', stopScanning);
    
    document.getElementById('retryButton').addEventListener('click', async () => {
      if (currentCameraId) {
        await startScanning(currentCameraId);
      } else {
        initializeCamera();
      }
    });

    async function initializeCamera() {
      updateStatus("Đang kiểm tra camera...");
      
      try {
        const cameras = await Html5Qrcode.getCameras();
        if (cameras && cameras.length > 0) {
          currentCameraId = cameras[0].id;
          await startScanning(currentCameraId);
          logEvent('camera_initialized');
        } else {
          showError("Không tìm thấy camera!");
          logEvent('no_camera_found');
        }
      } catch (error) {
        showError("Lỗi truy cập camera!");
        logEvent('camera_error', { error: error.message });
      }
    }

    window.addEventListener('beforeunload', () => {
      if (isScanning) {
        html5QrCode.stop().catch(console.error);
      }
    });

    document.addEventListener('DOMContentLoaded', () => {
      initializeScanner();
      initializeCamera();
      logEvent('page_view');
    });
  </script>
</body>
</html>
