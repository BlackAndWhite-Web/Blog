- 音频播放
    audio标签
    webAudio
        两者比较优差点
            aduio标签有浏览器限制，自动播放处理；
            webaudio可以拿到更底层的api做相关的处理;比如音频可视化等；
            webaudio还可以自制声音，对声音进行各种处理，比如混响等;

- 然后利用webaudio本身来进行play audio

- pixi-sound插件
    就是对音频播放采用webAudio来进行处理；
    最后暴露出的:soundlib, 
        sound.add =>  _sounds: map来维护; sound(media)
        点击play来进行进行play; sound(media).play
    AudioContext
    
