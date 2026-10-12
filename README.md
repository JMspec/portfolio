# 스마트팜 작물 질병 진단 API 서비스 (Plant Disease Detection API)

![Python](https://img.shields.io/badge/Python-3.9-blue) ![PyTorch](https://img.shields.io/badge/PyTorch-red) ![FastAPI](https://img.shields.io/badge/FastAPI-009688) ![Docker](https://img.shields.io/badge/Docker-2496ED) ![AWS](https://img.shields.io/badge/AWS_EC2-FF9900)

스마트폰으로 작물의 잎 사진을 촬영하면, 실시간으로 질병 여부와 종류를 진단해 주는 경량화된 AI 딥러닝 백엔드 API 서버입니다. 
농가의 엣지(Edge) 디바이스 환경을 고려하여 **추론 속도와 서버 리소스 최적화**에 집중했습니다.

---

## 1. 프로젝트 개요
*   **개발 인원:** 1인 (엔드투엔드 구축)
*   **주요 목표:** 
    *   PlantVillage 데이터셋(54,000+장)을 활용한 이미지 분류 모델 학습
    *   실무 환경을 고려한 경량화 모델(MobileNetV3) 선정 및 최적화
    *   FastAPI와 Docker를 활용한 독립적인 배포 파이프라인 구축

## 2. 시스템 아키텍처
1.  **AI Training (Local/GPU):** PyTorch로 데이터 증강 및 MobileNetV3 전이 학습 후 가중치(`.pt`) 추출
2.  **Backend (FastAPI):** 클라이언트의 이미지 업로드 요청을 비동기로 처리 및 AI 추론 결과 반환
3.  **Deployment (Docker/AWS EC2):** 환경 의존성을 없애기 위해 컨테이너화 후 클라우드 서버에 배포

## 3. API 사용 명세 (API Reference)

**엔드포인트:** `POST /predict`
**요청 (Request):** Multipart form-data 형태의 이미지 파일 (`file`)
**응답 (Response):** 
```json
{
  "predicted_class": "Tomato_Bacterial_spot",
  "confidence": 98.45
}
