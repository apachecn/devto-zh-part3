# 快速最小实时语音识别代码

> 原文：<https://dev.to/mishimay/swift-minimum-realtime-speech-recognition-code-4jc2>

## 准备

将这些设置添加到`Info.plist`。

```
 <key>NSSpeechRecognitionUsageDescription</key>
    <string>[description]</string>
    <key>NSMicrophoneUsageDescription</key>
    <string>[description]</string> 
```

## 代码

```
import UIKit
import Speech

class ViewController: UIViewController {

    private let speechRecognizer = SFSpeechRecognizer(locale: Locale(identifier: "ja-JP"))!
    private var recognitionRequest: SFSpeechAudioBufferRecognitionRequest?
    private var recognitionTask: SFSpeechRecognitionTask?
    private let audioEngine = AVAudioEngine()

    override func viewDidLoad() {
        super.viewDidLoad()

        requestRecognizerAuthorization()
        try? start()
    }

    private func requestRecognizerAuthorization() {
        SFSpeechRecognizer.requestAuthorization { (authStatus) in
        }
    }

    private func start() throws {
        if let recognitionTask = recognitionTask {
            recognitionTask.cancel()
            self.recognitionTask = nil
        }

        let audioSession = AVAudioSession.sharedInstance()
        try audioSession.setCategory(.record, mode: .measurement, options: [])
        try audioSession.setActive(true, options: .notifyOthersOnDeactivation)

        let recognitionRequest = SFSpeechAudioBufferRecognitionRequest()
        self.recognitionRequest = recognitionRequest
        recognitionRequest.shouldReportPartialResults = true
        recognitionTask = speechRecognizer.recognitionTask(with: recognitionRequest) { [weak self] (result, error) in
            guard let `self` = self else { return }

            var isFinal = false

            if let result = result {
                print(result.bestTranscription.formattedString)
                isFinal = result.isFinal
            }

            if error != nil || isFinal {
                self.audioEngine.stop()
                self.audioEngine.inputNode.removeTap(onBus: 0)
                self.recognitionRequest = nil
                self.recognitionTask = nil
            }
        }

        let recordingFormat = audioEngine.inputNode.outputFormat(forBus: 0)
        audioEngine.inputNode.installTap(onBus: 0, bufferSize: 1024, format: recordingFormat) { (buffer: AVAudioPCMBuffer, when: AVAudioTime) in
            self.recognitionRequest?.append(buffer)
        }

        audioEngine.prepare()
        try? audioEngine.start()
    }

    private func stop() {
        audioEngine.stop()
        recognitionRequest?.endAudio()
    }

} 
```

## 雨燕版

```
Apple Swift version 4.2 (swiftlang-1000.11.37.1 clang-1000.11.45.1)
Target: x86_64-apple-darwin18.2.0 
```

## 引用

*   [https://qiita.com/shu223/items/ca8e62803e83e396c177](https://qiita.com/shu223/items/ca8e62803e83e396c177)
*   [https://dev . class method . jp/smart phone/iphone/try-IOs 10-speech-recognizer/](https://dev.classmethod.jp/smartphone/iphone/try-ios10-speech-recognizer/)