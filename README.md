# yuuunas2-github.io2
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TGUISS BINGO</title>
    <style>
        body {
            text-align: center;
            font-family: Arial, sans-serif;
        }
        h1 {
            margin-top: 20px;
        }
        .roulette-container {
            position: relative;
            width: 300px;
            height: 300px;
            margin: 20px auto;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .image-display {
            max-width: 100%;
            max-height: 100%;
        }
        .spin-button {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        .upload-input {
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>TGUISS BINGO</h1>
    <input type="file" id="image-upload" class="upload-input" accept="image/*">
    <input type="text" id="image-text-input" placeholder="画像の説明を入力" class="upload-input">
    <button onclick="addImage()">追加</button>
    <button onclick="restartRoulette()">リスタート</button>
    <button onclick="resetRoulette()">リセット</button>
    <button onclick="generateShareLink()">共有リンク作成</button>
    <p id="share-link"></p>
    <div class="roulette-container">
        <img id="roulette-image" class="image-display" src="placeholder.jpg" alt="?">
    </div>
    <h2 id="image-text">???</h2>
    <button class="spin-button" onclick="spinRoulette()">回す！</button>
    
    <script>
        let originalImages = [];
        let images = [];

        function addImage() {
            const fileInput = document.getElementById("image-upload");
            const textInput = document.getElementById("image-text-input").value;
            
            if (fileInput.files.length === 0) {
                alert("画像を選択してください！");
                return;
            }
            
            const file = fileInput.files[0];
            const reader = new FileReader();
            reader.onload = function(e) {
                const imageData = { src: e.target.result, text: textInput || `画像${images.length + 1}` };
                originalImages.push(imageData);
                images.push(imageData);
                saveImages();
            };
            reader.readAsDataURL(file);
        }

        function spinRoulette() {
            if (images.length === 0) {
                alert("画像をアップロードしてください！");
                return;
            }
            let randomIndex = Math.floor(Math.random() * images.length);
            let selectedImage = images.splice(randomIndex, 1)[0];
            
            document.getElementById("roulette-image").src = selectedImage.src;
            document.getElementById("image-text").textContent = selectedImage.text;
        }

        function restartRoulette() {
            images = [...originalImages];
            document.getElementById("roulette-image").src = "placeholder.jpg";
            document.getElementById("image-text").textContent = "???";
        }

        function resetRoulette() {
            originalImages = [];
            images = [];
            localStorage.removeItem("savedImages");
            document.getElementById("roulette-image").src = "placeholder.jpg";
            document.getElementById("image-text").textContent = "???";
        }

        function generateShareLink() {
            if (originalImages.length === 0) {
                alert("画像を追加してください！");
                return;
            }
            localStorage.setItem("sharedImages", JSON.stringify(originalImages));
            const url = `${window.location.origin}${window.location.pathname}?shared=true`;
            document.getElementById("share-link").innerHTML = `<a href="${url}" target="_blank">共有リンク</a>`;
        }

        function loadImagesFromURL() {
            const params = new URLSearchParams(window.location.search);
            if (params.has("shared")) {
                const sharedData = localStorage.getItem("sharedImages");
                if (sharedData) {
                    originalImages = JSON.parse(sharedData);
                    images = [...originalImages];
                    saveImages();
                }
            }
        }

        function saveImages() {
            localStorage.setItem("savedImages", JSON.stringify(originalImages));
        }

        function loadSavedImages() {
            const savedData = localStorage.getItem("savedImages");
            if (savedData) {
                try {
                    originalImages = JSON.parse(savedData);
                    images = [...originalImages];
                } catch (e) {
                    console.error("ローカルデータの読み込みに失敗しました", e);
                }
            }
        }

        window.onload = function() {
            loadImagesFromURL();
            loadSavedImages();
        };
    </script>
</body>
</html>
