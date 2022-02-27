# web Audio API——音频可视化

### web Audio API

web Audio 提供的是模块化的API。基于web window创建出AudioContext，所有对音频的操作，都是基于AudioContext创建出AudioNode，然后将所有的AudioNode connect起来，就可以得到操作后的音频，形成最后的音频路由图，所有的操作都是基于AudioContext。

- AudioContext: 音频上下文，所有的操作都是基于该来进行，类似于canvas的context
- AudioNode: 按照类型来分，大体分为3类，Source Node, Effect Node Destination Node.
    - Source Node: 音频源节点，基本都是收集音频来源，可以是<Audio>标签， oscillator产生的声音，系统收集到的声音流。
    - Effect Node: 音频处理节点，如GainNode(音量控制节点)， AnalyzeNode(音频可视化节点)...
    - Destination Node: 音频输出节点，如扬声器...

![Untitled](web%20Audio%20API%E2%80%94%E2%80%94%E9%9F%B3%E9%A2%91%E5%8F%AF%E8%A7%86%E5%8C%96%20d18344b92fdc459a873a3f989a7f4818/Untitled.png)

利用web Audio API提供的一个振动器模块作为音频源，经过gainNode AudioNode的处理，最后在输出到系统扬声器进行播放

![Untitled](web%20Audio%20API%E2%80%94%E2%80%94%E9%9F%B3%E9%A2%91%E5%8F%AF%E8%A7%86%E5%8C%96%20d18344b92fdc459a873a3f989a7f4818/Untitled%201.png)

```jsx
const webAudioContext = new (window.AudioContext || window.webkitAudioContext)();

const oscilator = webAudioContext.createOscillator();
oscilator.type = 'square';
oscilator.frequency.value = 440;
oscilator.start();

const gainNode = webAudioContext.createGain();

gainNode.gain.value = 10;

oscilator.connect(gainNode);
gainNode.connect(webAudioContext.destination)
```

### Audio的专业术语

- 波形: 声音是由于震动产生, 声波是一种纵波，来回震动导致空气分子振动排列。我们取任意一个点，在一段时间内这个点的气压的变化。振幅越大的波形表示声音越大，单位时间内的频率大，表示音越高。
- 频谱：对波形进行傅里叶变换(FFT)，将音频信号转换为频域信息。

### Audio AnalyserNode

音频可视化，是将利用web Audio Api提供的effect node —— AnalyserNode获取到音频数据,  然后利用可视化渲染方案渲染出来。

![Untitled](web%20Audio%20API%E2%80%94%E2%80%94%E9%9F%B3%E9%A2%91%E5%8F%AF%E8%A7%86%E5%8C%96%20d18344b92fdc459a873a3f989a7f4818/Untitled%202.png)

### Code Demo 音频可视化

![Untitled](web%20Audio%20API%E2%80%94%E2%80%94%E9%9F%B3%E9%A2%91%E5%8F%AF%E8%A7%86%E5%8C%96%20d18344b92fdc459a873a3f989a7f4818/Untitled%203.png)

注意点：

- 将音频数据映射到analyserNode节点，是通过先定义好dataArray，bufferLength；analyserNode提供getByteFrequencyData，可以获取到当前音频播放的数据。**类似通过node connect，analyser事先定义容器装置数据，并提供相关api来获取播放过程中的数据，塞到定义的容器中**

```jsx
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <button class="play">play</button>
    <button class="stop">stop</button>
    <div>
        <canvas class="canvas" width="400" height="400"></canvas>
    </div>
</body>
<script>
const play = document.querySelector('.play');
const stop = document.querySelector('.stop');
const canvas = document.querySelector('.canvas');
// 创建 audio context
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
// 创建 audio buffer source node
const source = audioCtx.createBufferSource();
// 创建 audio effect node analyser
const analyser = audioCtx.createAnalyser();
// 设置analyser fft dataArray
analyser.fftSize = 256;
const bufferLength = analyser.frequencyBinCount;
const dataArray = new Uint8Array(bufferLength);
// audio node 关联
source.connect(analyser);
analyser.connect(audioCtx.destination);

const canvasCtx = canvas.getContext('2d');
canvasCtx.fillStyle = 'rgb(200, 200, 200)';
const width = 400, height = 400;
canvasCtx.fillRect(0, 0, width, height);
canvasCtx.lineWidth = 2;
canvasCtx.strokeStyle = 'rgb(0, 0, 0)';
canvasCtx.beginPath();

function getData() {
  var request = new XMLHttpRequest();
  request.open('GET', 'https://mdn.github.io/voice-change-o-matic/audio/concert-crowd.ogg', true);
  request.responseType = 'arraybuffer';
  request.onload = function() {
    var audioData = request.response;

    audioCtx.decodeAudioData(audioData, function(buffer) {
        source.buffer = buffer;
      });
  }
  request.send();
}
getData();

function draw() {
    //清除画布
    canvasCtx.clearRect(0, 0, width, height); 
    // 将当前频率数据复制到传入其中的Uint8Array
    analyser.getByteFrequencyData(dataArray); 
    requestAnimFrame = window.requestAnimationFrame(draw) || window.webkitRequestAnimationFrame(draw);
    canvasCtx.fillStyle = '#000130';
    canvasCtx.fillRect(0, 0, width, height);
    let barWidth = (width * 1 / bufferLength) * 2;
    let barHeight;
    let x = 0;
    for (let i = 0; i < bufferLength; i++) {
        barHeight = dataArray[i];
        canvasCtx.fillStyle = 'rgb(0, 255, 30)';
        canvasCtx.fillRect(x, height / 2 - barHeight / 2, barWidth, barHeight);
        x += barWidth + 1;
    }
};

play.onclick = function() {
    source.start(0);
    draw()
}

stop.onclick = function() {
    window.cancelAnimationFrame(requestAnimFrame)
    source.stop(0)
}
</script>
</html>
```