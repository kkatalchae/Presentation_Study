결론적으로 Bubble은 초보자이고 웹 앱을 빠르게 구축하고 싶을때.
FlutterFlow는 모바일 앱을 구축하고 싶고 장기적으로 다른 플랫폼을 이용하고 싶거나, 별도의 데이터베이스를 관리하는 데 무리가 없을 때. 

## FlutterFlow

- 목적
    - 네이티브 모바일 앱 제작에 중점.
    - 모바일 앱 개발에 더 최적화. 그러나 FlutterFlow를 사용하여 웹 앱을 구축할 수도 있음
- 모바일앱
    - FlutterFlow를 사용하면 앱을 Apple App Store 또는 Google Play Store에 직접 게시 가능
- 기능
    - 드래그 앤 드롭 기능을 사용하여 사용자 인터페이스를 구축
    - 프런트엔드 및 백엔드 구축을 지원하는 AI 기반 생성 기능을 제공
    - 더 많은 사용자 지정을 원하는 사용자를 위해 코드 편집기를 포함
- 가격
    - 표준 플랜($0)은 사용자가 코드를 다운로드하고 APK 다운로드 및 사용자 정의 도메인 웹 게시를 허용.
    - Pro 플랜($70/월)은 앱 스토어 배포 및 추가 기능을 허용.
    - Teams 플랜($70/월/사용자)은 실시간 협업 및 기타 팀 중심 기능을 허용.
    - 무료 체험판과 월간 플랜이 있지만 데이터베이스 사용량은 별도로 지불(예: Firebase). 전반적으로 FlutterFlow는 특히 사용량이 많은 앱의 경우 장기적으로 더 저렴할 수 있다.
- 데이터베이스
    - Firebase 또는 Superbase와 같은 별도의 백엔드 서비스가 필요
- 커뮤니티
    - 강력한 커뮤니티가 있음
- **코드 내보내기**
    - FlutterFlow는 코드를 내보낼 수 있지만 Bubble은 그렇지 않음. FlutterFlow를 사용하면 앱을 플랫폼에서 벗어나기로 결정할 경우 더 많은 유연성을 가지게 됨.

## Bubble

- 목적
    - 모바일 앱, 내부 도구, Chrome 확장 프로그램 등 더 광범위한 애플리케이션에 사용
    - 주로 웹 앱 개발에 중점
- 모바일앱
    - Bubble은 현재 이 기능이 없지만 추가 작업을 진행 중. Bubble 앱을 모바일 앱으로 게시하는 해결 방법이 있지만 이상적이지는 않음
    - expo 앱 패키징
- 기능
    - 빈 캔버스 방식으로 높은 수준의 사용자 지정을 허용
    - 추가 기능을 위해 다양한 플러그인과 통합
- 가격
    - 무료 플랜은 사용자가 앱을 구축하고 탐색할 수 있지만 기능을 제한
    - Starter 플랜($29/월)은 사용자 정의 도메인과 기본 기능을 사용하여 실시간 앱을 출시
    - Growth 플랜($119/월)은 더 많은 사용자, 편집자 및 앱 기능을 허용
    - Team 플랜($479/월)은 더 많은 사용자, 편집자 및 앱 기능을 허용
    - 무료 체험판과 월간 플랜이 포함. 또한 앱 사용량에 대한 요금인 작업 단위도 지불
- 데이터베이스
    - 데이터베이스를 플랫폼에 통합. 시작하기에 편리함
- 커뮤니티
    - 역사가 오래되어 커뮤니티 규모가 큼.
- **코드 내보내기**
    - FlutterFlow는 코드를 내보낼 수 있지만 Bubble은 그렇지 않음. FlutterFlow를 사용하면 앱을 플랫폼에서 벗어나기로 결정할 경우 더 많은 유연성을 가지게 됨.

## expo

- Expo는 단일 React 코드 기반으로 iOS, Android 및 웹에서 유니버설 네이티브 앱을 구축을 돕는 오픈 소스 플랫폼
- 일반적으로 개발자는 모바일 앱을 구축하려면 Apple의 경우 Swift 코드를, Android의 경우 Kotlin 코드를 작성해야.
- Expo는 자바스크립트 및 리액트를 사용하여 안드로이드, ios 및 웹용 범용 네이티브 앱을 만들 수 있음.
- 즉, JavaScript를 사용하여 진정한 네이티브 앱을 구축하고 카메라, 키보드 및 진동기와 같은 네이티브 API와 상호 작용할 수 있습니다.

### 사전 설정

안드로이드 스튜디오(android), xcode(ios) 설치 후 환경설정이 되어있어야. 

### APK 파일로 만들기 위한 빌드

1. eas.json

    ```
    {
    "build": {
        "preview": {
        "android": {
            "buildType": "apk"
        }
        },
        "preview2": {
        "android": {
            "gradleCommand": ":app:assembleRelease"
        }
        },
        "preview3": {
        "developmentClient": true
        },
        "preview4": {
        "distribution": "internal"
        },
        "production": {}
    }
    }
    ```

1. 빌드

    ```
    eas build -p android --profile preview
    ```

### 웹 뷰 만들기

<img width="512" alt="스크린샷 2024-05-26 오전 11 02 20" src="https://github.com/kkatalchae/Presentation_Study/assets/67645131/cb7cadc3-c99c-4675-8c63-869dc8ed98bd">

1. 라이브러리 설치
    
    ```php
    npx expo install react-native-webview
    ```
    
2. app.js
    
    ```php
    import { WebView } from 'react-native-webview';
    import Constants from 'expo-constants';
    import { StyleSheet } from 'react-native';
    
    export default function App() {
        return (
            <WebView
                source={{ uri: 'https://aotolove2.bubbleapps.io/' }}
            />
        );
    }
    
    ```