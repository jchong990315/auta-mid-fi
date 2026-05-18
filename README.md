# AURA — AI Ulcer Recognition Assistant

**상처지킴이 팀 · 간호정보시스템분석및설계 · Assignment #7**

| 이름 | 이메일 |
|------|--------|
| 정승준 | jchong990315@snu.ac.kr |
| 문윤서 | ysmoon@snu.ac.kr |
| 전효정 | megiminho@snu.ac.kr |

---

## 프로젝트 개요

AURA는 간호사와 보호자가 함께 사용하는 욕창 케어 보조 Android 앱입니다.  
간호사가 욕창 사진을 AI로 분석하고 체위변경 리마인더를 설정하면, 보호자 앱으로 FCM 푸시 알림이 발송됩니다.

- **대상 사용자**: 간호사, 욕창 환자 보호자  
- **플랫폼**: Android (API 26+)  
- **아키텍처**: MVVM + Clean Architecture  
- **언어**: Kotlin

---

## 구현 태스크

| Task | 설명 |
|------|------|
| Task 1 · 앱을 켜서 욕창을 확인한다 | 카메라/갤러리 → AI 분석 → 단계·신뢰도 결과 확인 |
| Task 2 · 앱을 켜서 욕창을 분석하고 설명한다 | 판별 근거(슬러프, 잠식, 전층손실) + 치료 권고를 탭으로 제공 |
| Task 3 · 앱을 켜서 욕창에 대한 리마인더를 보낸다 | 주기 설정 → FCM을 통한 보호자 앱 푸시 알림 전송 |

---

## 앱 사용 방법

### 간호사 — 욕창 판별
홈 화면에서 '욕창 판별'을 선택합니다. 카메라 버튼으로 욕창 부위를 촬영하거나 갤러리에서 사진을 선택합니다. 'AI 분석 시작' 버튼을 누르면 AURA가 욕창 단계와 신뢰도를 분석하여 결과 화면에 표시합니다.

### 간호사 — 분석 결과 확인
결과 화면에서 욕창 단계(1~4단계)와 신뢰도를 확인합니다. '판별 근거' 탭에서 전층 피부 손실, 슬러프 식별, 잠식 특징 등 AI 분석 근거를 확인하고, '치료 권고' 탭에서 드레싱 방법과 협진 권고 사항을 확인합니다.

### 간호사 — 환자 추가 및 리마인더 설정
홈 화면에서 '리마인더 설정'을 선택합니다. '+ 환자 추가' 버튼으로 이름, 성별, 나이, 병동, 진단명을 입력해 환자를 등록합니다. 환자 카드를 눌러 체위변경 리마인더로 이동한 뒤, 알림 주기(1~8시간)와 진동·소리 옵션을 설정하고 저장합니다.

### 보호자 — 알림 수신
간호사가 설정한 체위변경 리마인더 주기에 따라 보호자의 스마트폰으로 푸시 알림이 발송됩니다. 보호자는 알림을 통해 체위변경 시점을 확인하고, 필요 시 앱에서 체위변경 가이드(앙와위, 좌측위, 우측위)를 참고합니다.

---

## 디렉토리 아키텍처

```
// Android 프로젝트 구조 (Kotlin + MVVM + Clean Architecture)
app/
├── manifests/
│   └── AndroidManifest.xml
├── java/com/woundguard/aura/
│   ├── data/
│   │   ├── model/
│   │   │   ├── Patient.kt
│   │   │   ├── AnalysisResult.kt
│   │   │   └── Reminder.kt
│   │   ├── local/
│   │   │   ├── AppDatabase.kt       // Room DB
│   │   │   └── PatientDao.kt
│   │   ├── remote/
│   │   │   ├── ApiService.kt        // Retrofit
│   │   │   └── FcmService.kt        // Firebase FCM
│   │   └── repository/
│   │       ├── PatientRepository.kt
│   │       └── AnalysisRepository.kt
│   ├── domain/
│   │   └── usecase/
│   │       ├── AnalyzeUlcerUseCase.kt
│   │       ├── AddPatientUseCase.kt
│   │       └── SendReminderUseCase.kt
│   ├── ui/
│   │   ├── MainActivity.kt
│   │   ├── home/
│   │   │   ├── HomeFragment.kt
│   │   │   └── HomeViewModel.kt
│   │   ├── analysis/
│   │   │   ├── AnalysisFragment.kt  // Task 1
│   │   │   ├── ResultFragment.kt    // Task 2
│   │   │   └── AnalysisViewModel.kt
│   │   ├── patient/
│   │   │   ├── PatientListFragment.kt
│   │   │   ├── PatientDetailFragment.kt
│   │   │   └── PatientViewModel.kt
│   │   ├── reminder/
│   │   │   ├── ReminderFragment.kt  // Task 3
│   │   │   └── ReminderViewModel.kt
│   │   └── guide/
│   │       └── GuideFragment.kt
│   ├── di/                          // Hilt DI
│   │   ├── AppModule.kt
│   │   └── NetworkModule.kt
│   └── AuraApplication.kt
└── res/
    ├── layout/
    │   ├── fragment_home.xml
    │   ├── fragment_analysis.xml
    │   ├── fragment_result.xml
    │   ├── fragment_patient_list.xml
    │   ├── fragment_reminder.xml
    │   └── fragment_guide.xml
    ├── drawable/
    ├── values/
    │   ├── colors.xml
    │   ├── strings.xml
    │   └── themes.xml
    └── navigation/
        └── nav_graph.xml
```

---

## 기술 스택

```kotlin
// build.gradle.kts (app)
dependencies {
    // UI & Navigation
    implementation("androidx.navigation:navigation-fragment-ktx:2.7.7")
    implementation("com.google.android.material:material:1.12.0")

    // Architecture (ViewModel, Room)
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.8.2")
    implementation("androidx.room:room-ktx:2.6.1")
    kapt("androidx.room:room-compiler:2.6.1")

    // Network (Retrofit + OkHttp)
    implementation("com.squareup.retrofit2:retrofit:2.11.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.12.0")

    // AI / ML (TensorFlow Lite)
    implementation("org.tensorflow:tensorflow-lite:2.16.1")
    implementation("org.tensorflow:tensorflow-lite-support:0.4.4")

    // Push Notification (Firebase FCM)
    implementation("com.google.firebase:firebase-messaging-ktx:24.0.0")

    // Dependency Injection (Hilt)
    implementation("com.google.dagger:hilt-android:2.51.1")
    kapt("com.google.dagger:hilt-compiler:2.51.1")

    // Camera (CameraX)
    implementation("androidx.camera:camera-camera2:1.3.4")
    implementation("androidx.camera:camera-lifecycle:1.3.4")
    implementation("androidx.camera:camera-view:1.3.4")
}
```

---

## 프로토타입 한계점

> 이 프로토타입은 Medium-fi 수준으로, 실제 기능이 아닌 사용자 경험(UX)과 UI 흐름을 시뮬레이션합니다.

- **AI 분석은 시뮬레이션입니다.** TFLite 모델 미탑재, 결과값 하드코딩(3단계, 92.4%).  
- **카메라/갤러리는 UI 시뮬레이션입니다.** 실기기 카메라 미연결.  
- **FCM 알림은 구현되지 않았습니다.** 저장 및 전송 기능 미작동.  
- **데이터는 세션 내에서만 유지됩니다.** Room DB 미연동, 새로고침 시 초기화.
