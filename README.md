# Hyundai HR Attrition Analytics

## 1. 프로젝트 개요

- 목적 : 직원 이직 리스크 정리 및 HR 검토 우선순위 기준 작성
- 주제 : IBM HR Attrition 데이터를 활용한 이직 리스크 분석
- 구성 방향 : 이직 확률 산출, 리스크 세그먼트 구분, 개입 우선순위 정리
- 주요 산출물 : risk score, 리스크 세그먼트, 개입 우선순위 표

## 2. 문제 정의

- 직원 이직 여부 예측만으로는 실제 활용 범위 제한적
- HR 관점에서는 누가 상대적으로 더 높은 리스크를 보이는지, 어떤 집단을 먼저 볼 필요가 있는지에 대한 정리 필요
- 분석 목표 : 이직 리스크를 추정하고 세그먼트 및 개입 우선순위 기준까지 함께 정리

## 4. 분석 설계

- 1단계 : 기초 EDA 수행
- 2단계 : 근무부담, 보상 수준, 조직 정착도 관련 파생변수 설계
- 3단계 : 로지스틱 회귀 baseline 모델 학습
- 4단계 : LightGBM 및 AutoML 적용 후 성능 비교
- 5단계 : OOF 기반 risk score 산출
- 6단계 : High / Medium / Low Risk 세그먼트 구분
- 7단계 : 핵심인재 proxy와 결합한 개입 우선순위 정리

## 7. 모델 비교 결과

- 로지스틱 회귀 baseline
  - ROC-AUC : 0.8348
  - PR-AUC : 0.6518
  - 이직자 recall : 0.7234

- LightGBM baseline
  - ROC-AUC : 0.8054
  - PR-AUC : 0.5431
  - 이직자 recall : 0.3830

- AutoML
  - Best estimator : lgbm
  - ROC-AUC : 0.7737
  - PR-AUC : 0.4717
  - 이직자 recall : 0.2979

- 최종 모델 : 로지스틱 회귀 선정
- 선정 이유 : 성능, 해석 용이성, 세그먼트 활용 측면 종합 고려
- 확인 사항 : 현재 데이터 구조에서는 복잡한 모델보다 로지스틱 회귀가 더 안정적인 결과 제공

## 8. 리스크 세그먼트 설계

- risk score 산출 방식 : OOF(out-of-fold) 예측확률 사용
- 적용 이유 : 동일 데이터 학습/예측에 따른 점수 과대평가 가능성 완화
- 세그먼트 기준
  - High Risk : 상위 10%
  - Medium Risk : 상위 10~30%
  - Low Risk : 나머지

- 세그먼트별 실제 이직률
  - High Risk : 66.7%
  - Medium Risk : 26.9%
  - Low Risk : 5.8%

## 9. 개입 우선순위 설계

- 핵심인재 proxy 정의 기준
  - JobLevel >= 3
  - PerformanceRating >= 4
  - MonthlyIncome 상위 25%
  - 위 3개 조건 중 2개 이상 충족 시 핵심인재 proxy 부여

- 개입 우선순위 정의
  - High Risk + 핵심인재 proxy : 즉시 유지 개입
  - High Risk + 비핵심 : 선별 검토 대상
  - Medium Risk + 핵심인재 proxy : 예방적 케어 대상
  - Medium Risk + 비핵심 : 모니터링 대상
  - Low Risk + 핵심인재 proxy : 핵심인재 일반 케어
  - 기타 : 일반 모니터링

## 10. 핵심 인사이트

- 초과근무, 출장 빈도, 낮은 보상 수준, 짧은 재직기간과 이직 리스크 간 연관성 확인
- 현재 데이터에서는 복잡한 비선형 모델보다 로지스틱 회귀가 더 안정적인 결과 제공
- 이직 분석에서는 모델 성능 외에 변수 구성과 결과 활용 방식도 함께 중요
- 예측 결과는 리스크 세그먼트와 개입 우선순위 기준으로 정리

## 11. 한계 및 확장 방향

- 실제 기업 HR 데이터 대비 이벤트 이력 정보 부족
- 조직 이동, 리더 변경, 보상 인상 이력 등 실제 HR 이벤트 부재
- 핵심인재 여부는 proxy 기준으로 정의한 한계 존재
- 향후 확장 방향
  - 실제 이벤트 로그 기반 상태전이 분석 적용
  - 시계열/생존분석 기반 이탈 시점 예측 적용
  - SHAP 또는 개별 리스크 설명 리포트 확장 가능

## 12. 폴더 구조

```text
hyundai-hr-attrition-analytics/
├── README.md
├── requirements.txt
├── data/
│   ├── raw/
│   └── processed/
├── notebooks/
├── outputs/
│   ├── figures/
│   └── tables/
└── src/