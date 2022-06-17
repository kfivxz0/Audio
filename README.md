# 음악 재생하고 녹음하기 (13장)
오디오 파일을 재생할 수 있다면 벨소리나 알람과 같이 각종 소리와 관련된 다양한 작업을 할 수 있습니다. 오디오를 재생하는 가장 쉬운 방법은 AVAudioPlayer를 사용하는 것입니다. 
AVAudioPlayer를 사용하면 오디오 파일을 재생, 일시 정지, 볼륨을 조절할 수 있고, 녹음을 할 수 있습니다.

---
## 완성된 모습
![스크린샷 2022-05-20 오후 4 41 11](https://user-images.githubusercontent.com/106981296/173380704-6e06042c-db84-4086-9a4e-cc5c5b01bca0.png)


---
## 주요 기능
* 음악 재생하기
* 일시 정지하기
* 볼륨 조절하기
* 녹음하기

---
## 전체 소스
![스크린샷 2022-05-20 오후 4 42 10](https://user-images.githubusercontent.com/106981296/173381241-d6875dec-c6e0-44f9-87e8-7d4defb16743.png)
![스크린샷 2022-05-20 오후 4 42 26](https://user-images.githubusercontent.com/106981296/173381294-e88ba303-c843-475b-addc-b37291b4d72f.png)
![스크린샷 2022-05-20 오후 4 42 38](https://user-images.githubusercontent.com/106981296/173381420-75d3e202-58b6-417e-9dd9-68beef72709d.png)
![스크린샷 2022-05-20 오후 4 42 57](https://user-images.githubusercontent.com/106981296/173381479-54817f9b-0077-41e9-9a99-09aab7b471e0.png)

---
## 주요 코드 해석

```SWIFT
// 오디오를 재생하기 위해 헤더파일과 델리게이트가 필요하므로 'AVFoundation'을 불러오고, 'AVAudioPlayerDelegate'선언을 추가합니다.
import UIKit
import AVFoundation

class ViewController: UIViewController, AVAudioPlayerDelegat{}
```

클래스에서 사용할 변수와 상수를 선언합니다.
```SWIFT
// AVAudioPlayer 인스턴트 변수
var audioPlayer : AVAudioPlayer!

// 재생할 오디오의 파일명 변수
var audioFile : URL!

// 최대 볼륨, 실수형 상수
let MAX_VOLUME : Float = 10.0

// 타이머를 위한 변수
var progressTimer : Timer!
```

오디오 파일을 추가한 뒤 audioFile의 변수로 설정합니다.
```SWIFT
audioFile = Bundle.main.url(forResource: "Sicilian_Breeze", withExtension: "mp3")
```


오디오 재생을 위한 초기화하기 위해 함수를 추가합니다.
```SWIFT
func initPlay(){}

//do-try-catch문을 사용해 오디오파일이 없을 때에 대비합니다.
do{
    audioPlayer = try AVAudioPlayer(contentsOf: audioFile
    }catch let error as NSError{
      print("Error-initPlay : \(error)")
    }
}
```

재생 시간 초기화하기
```SWIFT
// "00:00"형태로 바꾸기위해 TimeInterval 값을 받아 문자열로 돌려보내는 함수를 생성합니다.
func convertNSTimeInterval2String(_ time:TimeInterval) -> String{}

//time 값을 60으로 나눈 몫을 정수 값으로 변환해 상수 min 값에 초기화합니다.
let min = Int(time/60)

//time 값을 60으로 나눈 나머지 값을 정수 값으로 변환해 상수 sec 값에 초기화 합니다.
let sec = Int(time.truncatingRemainder(dividingBy: 60))

//이 두 값을 활용해 %02d:%02d 형태의 문자열로 변환하여 strTime에 초기화합니다.
let strTime = String(format: "%02d:%02d", min, sec)
```


```SWIFT
//재생 시간을 lblEndTime의 텍스트에 출력하고, lblCurrentTime에는 00:00가 출력 되도록 0의 값을 입력합니다.
lblEndTime.text = convertNSTimeInterval2String(audioPlayer.duration)
lblCurrentTime.text = convertNSTimeInterval2String(0)
```

재생, 일시 정지, 정지 버튼을 제어합니다.
```SWIFT
// 재생, 일시 정지, 정지 버튼의 동작 여부를 설정하는 함수를 추가한다.
func setPlayButtons(_ play:Bool, pause:Bool, stop:Bool){
btnPlay.isEnabled = play
btnPause.isEnabled = pause
btnStop.isEnabled = stop
}
```

재생이 끝나면 초기 상태로 돌아가도록 함수를 추가합니다.
```SWIFT
// 타이머를 무효화합니다.
func audioPlayerDidFinishPlaying(_ player: AVAudioPlayer, successfully flag: Bool){
progressTimer.invalidate()
//재생 버튼은 활성화하고 나머지 버튼은 비활성화합니다.
setPlayButtons(true, pause: false, stop: false)
}
```

녹음을 위한 상수와 변수를 선언합니다.
```SWIFT
var audioRecorder : AVAudioRecorder !
var isRecordMode = false
```

```SWIFT
//녹음을 했는데 재생 파일에 겹쳐서 저장되면 안되기 때문에 모드에 따라 다른 파일을 선택하기 위해 함수를 추가합니다.
// audioFile은 재생할 때, 즉 녹음 모드가 아닐 때 사용하므로 if문에 넣습니다.
func selectAudioFile(){
if !isRecordMode
}
```


녹음 음질, 오디오 채널, 샘플률을 설정합니다.
```SWIFT
AVEncoderBitRateKey : 320000,
AVNumberOfChannelsKey : 2,
AVSampleRateKey : 441000.0
```

updateRecordTime 함수를 생성합니다. 타이머에 의해 0.1초 간격으로 이 함수를 실행하는데, 그 때마다 녹음 시간이 표시됩니다.
```SWIFT
@objc func updateRecordTime(){
lblRecordTime.text = convertNSTimeInterval2String(audioRecorder.currentTime)
}
```

---
출처: Do it! 스위프트로 아이폰 앱 만들기 입문
