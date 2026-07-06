<div align="center">

# 🏋️ ShadowFit

**카메라로 찍은 내 운동 자세를, 정답 동작과 실시간으로 비교해주는 홈트레이닝 앱**

사용자가 스마트폰 카메라로 스쿼트 동작을 촬영하면, AI 서버가 자세를 추출해 기준(레퍼런스) 동작과 시계열로 비교(DTW)하고, 그 결과를 실시간 피드백(음성 안내 포함)으로 되돌려줍니다.
React Native 앱 ↔ Spring Boot 백엔드 ↔ FastAPI AI 서버가 gRPC로 연동되는 구조입니다.



</div>

---

<div align="center">

# 🛠️ 기술 스택

## 📱 Frontend

| 역할 | 종류 |
| :--- | :--- |
| **Framework & Runtime** | ![React Native](https://img.shields.io/badge/REACT_NATIVE_0.81-61DAFB?style=for-the-badge&logo=react&logoColor=black) ![React](https://img.shields.io/badge/REACT_19-61DAFB?style=for-the-badge&logo=react&logoColor=black) ![Expo](https://img.shields.io/badge/EXPO_SDK_54-000020?style=for-the-badge&logo=expo&logoColor=white) |
| **Routing** | ![Expo Router](https://img.shields.io/badge/EXPO_ROUTER_6-000020?style=for-the-badge&logo=expo&logoColor=white) |
| **Language** | ![TypeScript](https://img.shields.io/badge/TYPESCRIPT_5.9-3178C6?style=for-the-badge&logo=typescript&logoColor=white) |
| **State Management** | ![Zustand](https://img.shields.io/badge/ZUSTAND-443E38?style=for-the-badge&logo=react&logoColor=white) ![TanStack Query](https://img.shields.io/badge/TANSTACK_QUERY-FF4154?style=for-the-badge&logo=reactquery&logoColor=white) |
| **Networking** | ![Axios](https://img.shields.io/badge/AXIOS-5A29E4?style=for-the-badge&logo=axios&logoColor=white) |
| **Storage** | ![AsyncStorage](https://img.shields.io/badge/ASYNC_STORAGE-000000?style=for-the-badge&logo=react&logoColor=white) ![SecureStore](https://img.shields.io/badge/SECURE_STORE-000020?style=for-the-badge&logo=expo&logoColor=white) |
| **Navigation & UI** | ![React Navigation](https://img.shields.io/badge/REACT_NAVIGATION-000000?style=for-the-badge&logo=react&logoColor=white) ![Reanimated](https://img.shields.io/badge/REANIMATED-FFCC00?style=for-the-badge&logo=react&logoColor=black) ![Gesture Handler](https://img.shields.io/badge/GESTURE_HANDLER-000000?style=for-the-badge&logo=react&logoColor=white) ![Safe Area Context](https://img.shields.io/badge/SAFE_AREA_CONTEXT-111111?style=for-the-badge&logo=react&logoColor=white) |
| **Design & Graphics** | ![Linear Gradient](https://img.shields.io/badge/LINEAR_GRADIENT-FF6B6B?style=for-the-badge) ![SVG](https://img.shields.io/badge/SVG-FFB13B?style=for-the-badge&logo=svg&logoColor=black) ![Expo Vector Icons](https://img.shields.io/badge/VECTOR_ICONS-000020?style=for-the-badge&logo=expo&logoColor=white) |
| **Media & Device Features** | ![Expo Camera](https://img.shields.io/badge/EXPO_CAMERA-000020?style=for-the-badge&logo=expo&logoColor=white) ![Image Picker](https://img.shields.io/badge/IMAGE_PICKER-000020?style=for-the-badge&logo=expo&logoColor=white) ![Media Library](https://img.shields.io/badge/MEDIA_LIBRARY-000020?style=for-the-badge&logo=expo&logoColor=white) ![Expo AV](https://img.shields.io/badge/EXPO_AV-000020?style=for-the-badge&logo=expo&logoColor=white) ![Expo Speech](https://img.shields.io/badge/EXPO_SPEECH-000020?style=for-the-badge&logo=expo&logoColor=white) ![Expo Haptics](https://img.shields.io/badge/EXPO_HAPTICS-000020?style=for-the-badge&logo=expo&logoColor=white) |
| **Visualization & Components** | ![Calendars](https://img.shields.io/badge/CALENDARS-4285F4?style=for-the-badge) ![Chart Kit](https://img.shields.io/badge/CHART_KIT-34A853?style=for-the-badge) ![Slider](https://img.shields.io/badge/SLIDER-FF9900?style=for-the-badge) |
| **Web Support** | ![React Native Web](https://img.shields.io/badge/REACT_NATIVE_WEB-61DAFB?style=for-the-badge&logo=react&logoColor=black) ![React DOM](https://img.shields.io/badge/REACT_DOM-61DAFB?style=for-the-badge&logo=react&logoColor=black) |

---

## ⚙️ Backend

| 역할 | 종류 |
| :--- | :--- |
| **Language** | ![Java](https://img.shields.io/badge/JAVA_21-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white) |
| **Framework** | ![Spring Boot](https://img.shields.io/badge/SPRING_BOOT_3.4-6DB33F?style=for-the-badge&logo=springboot&logoColor=white) ![Spring WebFlux](https://img.shields.io/badge/WEBFLUX-6DB33F?style=for-the-badge&logo=spring&logoColor=white) |
| **Database / ORM** | ![MySQL](https://img.shields.io/badge/MYSQL_8-4479A1?style=for-the-badge&logo=mysql&logoColor=white) ![JPA](https://img.shields.io/badge/JPA-59666C?style=for-the-badge&logo=hibernate&logoColor=white) ![H2](https://img.shields.io/badge/H2_DATABASE-09476B?style=for-the-badge&logo=h2&logoColor=white) |
| **Security** | ![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white) ![Spring Security](https://img.shields.io/badge/SPRING_SECURITY-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white) |
| **API Docs** | ![Swagger](https://img.shields.io/badge/SWAGGER-85EA2D?style=for-the-badge&logo=swagger&logoColor=black) |
| **Service 간 통신** | ![gRPC](https://img.shields.io/badge/GRPC-244C5A?style=for-the-badge&logo=grpc&logoColor=white) ![Protocol Buffers](https://img.shields.io/badge/PROTOBUF-4285F4?style=for-the-badge&logo=google&logoColor=white) |
| **Utilities** | ![Lombok](https://img.shields.io/badge/LOMBOK-BC4521?style=for-the-badge&logo=java&logoColor=white) ![ModelMapper](https://img.shields.io/badge/MODELMAPPER-FF6F00?style=for-the-badge&logo=java&logoColor=white) |

---

## 🤖 AI Server

| 역할 | 종류 |
| :--- | :--- |
| **Language** | ![Python](https://img.shields.io/badge/PYTHON-3776AB?style=for-the-badge&logo=python&logoColor=white) |
| **Framework** | ![FastAPI](https://img.shields.io/badge/FASTAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white) ![Uvicorn](https://img.shields.io/badge/UVICORN-4051B5?style=for-the-badge&logo=uvicorn&logoColor=white) |
| **AI / CV** | ![MediaPipe](https://img.shields.io/badge/MEDIAPIPE-FF6F00?style=for-the-badge&logo=google&logoColor=white) ![OpenCV](https://img.shields.io/badge/OPENCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white) ![PyTorch](https://img.shields.io/badge/PYTORCH-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white) |
| **Motion Analysis** | ![DTW](https://img.shields.io/badge/DTW-DYNAMIC_TIME_WARPING-blue?style=for-the-badge) |
| **Validation** | ![Pydantic](https://img.shields.io/badge/PYDANTIC-E92063?style=for-the-badge&logo=pydantic&logoColor=white) |
| **Service 간 통신** | ![gRPC](https://img.shields.io/badge/GRPC-244C5A?style=for-the-badge&logo=grpc&logoColor=white) ![Protocol Buffers](https://img.shields.io/badge/PROTOBUF-4285F4?style=for-the-badge&logo=google&logoColor=white) |

---

## 🐳 Infra & 배포

| 역할 | 종류 |
| :--- | :--- |
| **Container** | ![Docker](https://img.shields.io/badge/DOCKER-2496ED?style=for-the-badge&logo=docker&logoColor=white) ![Docker Compose](https://img.shields.io/badge/DOCKER_COMPOSE-2496ED?style=for-the-badge&logo=docker&logoColor=white) |
| **Web Server** | ![Nginx](https://img.shields.io/badge/NGINX-009639?style=for-the-badge&logo=nginx&logoColor=white) |
| **Cloud** | ![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white) |
| **CI/CD** | ![GitHub Actions](https://img.shields.io/badge/GITHUB_ACTIONS-2088FF?style=for-the-badge&logo=githubactions&logoColor=white) |
| **Service 간 프로토콜** | ![REST API](https://img.shields.io/badge/REST_API-005571?style=for-the-badge) ![gRPC](https://img.shields.io/badge/GRPC-244C5A?style=for-the-badge&logo=grpc&logoColor=white) |

</div>
