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

## 3. 핵심 문제 해결 (Troubleshooting) 

이 프로젝트를 진행하며 다음 세 가지 기술적 문제를 해결했습니다.

### ① 실환경 노이즈 대비 강건한 모델 학습 (Data Augmentation)
*   **문제:** 스마트팜 현장에서 찍힌 사진은 조명이나 각도가 일정하지 않음.
*   **해결:** 학습 시 `RandomResizedCrop`, `RandomHorizontalFlip` 등의 증강 기법을 적용하여 모델이 객체의 다양한 형태에 적응하도록 유도함. (검증 정확도 향상 기여)

### ② 클라우드 프리티어 메모리 초과 문제 해결 (Resource Optimization)
*   **문제:** RAM 1GB인 AWS EC2 프티리어 환경에서 무거운 AI 서버 구동 시 메모리 부족(OOM)으로 서버 다운 발생.
*   **해결:** 
    *   ResNet 대비 파라미터 수가 적고 추론이 빠른 **MobileNetV3-Small**로 아키텍처 변경.
    *   Dockerfile 작성 시 `python:3.9-slim` 베이스 이미지를 사용하고, 용량이 큰 CUDA 버전 PyTorch 대신 **CPU 전용 PyTorch**를 설치하도록 명시하여 컨테이너 이미지를 약 2GB 이상 경량화함.

### ③ 추론 시 텐서 차원 불일치 에러 (Data Preprocessing)
*   **문제:** 단일 이미지 추론 시 모델이 4차원 텐서를 요구하여 에러 발생.
*   **해결:** PyTorch의 `unsqueeze(0)`을 활용해 단일 이미지에 임시 배치(Batch) 차원을 추가하여 안정적인 추론 파이프라인 구축.

## 4. API 사용 명세 (API Reference)

**엔드포인트:** `POST /predict`
**요청 (Request):** Multipart form-data 형태의 이미지 파일 (`file`)
**응답 (Response):** 
```json
{
  "predicted_class": "Tomato_Bacterial_spot",
  "confidence": 98.45
}
