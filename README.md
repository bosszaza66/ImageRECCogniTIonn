<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Teachable Machine Image Model</title>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
    <style>
        body {
            background-color: #fff;
            color: #333;
            font-family: 'Segoe UI', sans-serif;
            text-align: center;
            margin: 0;
            padding: 30px;
        }

        h1 {
            font-size: 2rem;
            color: #ff6600; /* สีส้มสด */
            margin-bottom: 20px;
        }

        button {
            background-color: #ff6600; /* สีส้มสด */
            color: #fff;
            border: 1px solid #e65c00;
            padding: 12px 24px;
            font-size: 1rem;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s;
            font-weight: bold;
        }

        button:hover {
            background-color: #e65c00;
        }

        #webcam-container {
            margin: 20px auto;
            border: 3px solid #ff6600; /* ขอบสีส้ม */
            border-radius: 8px;
            overflow: hidden;
            width: 250px;
            height: 250px;
        }

        #label-container {
            margin-top: 30px;
        }

        #label-container div {
            padding: 8px 0;
            font-size: 1.1rem;
            border-bottom: 1px solid #ddd;
            color: #333;
        }
    </style>
</head>

<body>
    <h1>Teachable Machine Image Model</h1>
    <button type="button" onclick="init()">Start</button>
    <div id="webcam-container"></div>
    <div id="label-container"></div>

    <script type="text/javascript">
        const URL = "https://teachablemachine.withgoogle.com/models/R91WZ_jhDP/";

        let model, webcam, labelContainer, maxPredictions;

        async function init() {
            const modelURL = URL + "model.json";
            const metadataURL = URL + "metadata.json";

            model = await tmImage.load(modelURL, metadataURL);
            maxPredictions = model.getTotalClasses();

            const flip = true;
            webcam = new tmImage.Webcam(200, 200, flip);
            await webcam.setup();
            await webcam.play();
            window.requestAnimationFrame(loop);

            document.getElementById("webcam-container").appendChild(webcam.canvas);
            labelContainer = document.getElementById("label-container");
            labelContainer.innerHTML = "";
            for (let i = 0; i < maxPredictions; i++) {
                labelContainer.appendChild(document.createElement("div"));
            }
        }

        async function loop() {
            webcam.update();
            await predict();
            window.requestAnimationFrame(loop);
        }

        async function predict() {
            const prediction = await model.predict(webcam.canvas);
            for (let i = 0; i < maxPredictions; i++) {
                const classPrediction =
                    prediction[i].className + ": " + prediction[i].probability.toFixed(2);
                labelContainer.childNodes[i].innerHTML = classPrediction;
            }
        }
    </script>
</body>

</html>
