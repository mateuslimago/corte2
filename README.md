# corte2<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Image Carousel Splitter</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
      background-color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }

    .container {
      text-align: center;
      padding: 2rem;
      box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
      border-radius: 16px;
      background-color: #f8f9fa;
      max-width: 480px;
      width: 90%;
    }

    .logo {
      font-size: 32px;
      color: #4285f4;
      font-weight: 600;
      margin-bottom: 24px;
    }

    .upload-area {
      border: 2px dashed #ccc;
      border-radius: 10px;
      padding: 30px;
      color: #666;
      cursor: pointer;
      margin-bottom: 20px;
      transition: border-color 0.3s ease;
    }

    .upload-area.dragover {
      border-color: #4285f4;
      color: #4285f4;
    }

    .controls {
      display: flex;
      justify-content: center;
      align-items: center;
      margin-bottom: 20px;
      gap: 10px;
    }

    .controls input {
      width: 50px;
      text-align: center;
      font-size: 16px;
      padding: 5px;
    }

    .controls button {
      padding: 5px 12px;
      font-size: 16px;
      cursor: pointer;
      border: none;
      background-color: #e0e0e0;
      border-radius: 5px;
      transition: background-color 0.3s;
    }

    .controls button:hover {
      background-color: #d0d0d0;
    }

    .action-button {
      background-color: #4285f4;
      color: #fff;
      border: none;
      padding: 10px 25px;
      font-size: 16px;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    .action-button:hover {
      background-color: #3367d6;
    }

    img {
      max-width: 100%;
      max-height: 200px;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="logo">Image Splitter</div>
    <div class="upload-area" id="uploadArea">
      <p>Drag & Drop or Click to Upload an Image</p>
    </div>
    <div class="controls">
      <button id="decrease">-</button>
      <input type="number" id="slices" value="3" min="1" />
      <button id="increase">+</button>
    </div>
    <button class="action-button" id="sliceButton">FATIAR</button>
  </div>

  <script>
    const uploadArea = document.getElementById('uploadArea');
    const sliceButton = document.getElementById('sliceButton');
    const slicesInput = document.getElementById('slices');
    const increaseBtn = document.getElementById('increase');
    const decreaseBtn = document.getElementById('decrease');

    let uploadedImage = null;
    let imageName = 'imagem';

    uploadArea.addEventListener('click', () => {
      const fileInput = document.createElement('input');
      fileInput.type = 'file';
      fileInput.accept = 'image/*';
      fileInput.onchange = event => {
        handleFileUpload(event.target.files[0]);
      };
      fileInput.click();
    });

    uploadArea.addEventListener('dragover', (event) => {
      event.preventDefault();
      uploadArea.classList.add('dragover');
    });

    uploadArea.addEventListener('dragleave', () => {
      uploadArea.classList.remove('dragover');
    });

    uploadArea.addEventListener('drop', (event) => {
      event.preventDefault();
      uploadArea.classList.remove('dragover');
      handleFileUpload(event.dataTransfer.files[0]);
    });

    function handleFileUpload(file) {
      if (file && file.type.startsWith('image/')) {
        imageName = file.name.split('.').slice(0, -1).join('.') || 'imagem';
        const reader = new FileReader();
        reader.onload = (e) => {
          uploadedImage = e.target.result;
          uploadArea.innerHTML = `<img src="${uploadedImage}" alt="Uploaded Image">`;
        };
        reader.readAsDataURL(file);
      } else {
        alert('Por favor, envie um arquivo de imagem válido.');
      }
    }

    increaseBtn.addEventListener('click', () => {
      slicesInput.value = parseInt(slicesInput.value) + 1;
    });

    decreaseBtn.addEventListener('click', () => {
      if (parseInt(slicesInput.value) > 1) {
        slicesInput.value = parseInt(slicesInput.value) - 1;
      }
    });

    sliceButton.addEventListener('click', () => {
      if (!uploadedImage) {
        alert('Por favor, envie uma imagem primeiro.');
        return;
      }

      const slices = parseInt(slicesInput.value);
      const img = new Image();
      img.src = uploadedImage;
      img.onload = () => {
        const canvas = document.createElement('canvas');
        const sliceWidth = img.width / slices;

        for (let i = 0; i < slices; i++) {
          canvas.width = sliceWidth;
          canvas.height = img.height;
          const ctx = canvas.getContext('2d');
          ctx.clearRect(0, 0, canvas.width, canvas.height);
          ctx.drawImage(img, i * sliceWidth, 0, sliceWidth, img.height, 0, 0, sliceWidth, img.height);

          const a = document.createElement('a');
          a.href = canvas.toDataURL();
          a.download = `${imageName} - slide ${i + 1}.png`;
          a.click();
        }
        alert('Imagens cortadas e baixadas com sucesso!');
      };
    });
  </script>
</body>
</html>
