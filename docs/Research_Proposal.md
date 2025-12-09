## 1.프로젝트 개요(Overview)
#### 프로젝트 제목 : LANL-TGN-NeuroSym
#### 목표 : LANL unified 데이터에 TGN과 Neurosymbolic AI를 적용하여 향상된 IDS모델 개발
#### 기대 효과 : IDS 성능이 향상된 모델, 탐지 결과에 대한 Neurosymbolic explanability 추가

## 2.문제 정의 (Problem Definition)
#### 현재 문제점
1. 전통적인 IDS(rule 기반)는 시간적/공간적(그래프 구조적)인 특징을 파악하지 못함.
3. AI를 활용한 IDS모델들은 탐지에 대한 explain이 부족함. 이는 설명가능성이 중요한 보안 domain에서 중요한 문제가 됨.
#### 프로젝틀 통해 해결할 문제
1. TGN모델을 이용하여 개발한 IDS는 시간적/공간적(그래프 구조적) 특징을 얼마나 잘 포착하는가?
2. Neurosymbolic rule 추가 시 성능/해석가능성이 향상되는가?

## 3. 관련 연구(Related Work)
- TGN (Rossi et al., 2020): event-based temporal GNN
- Jbeil et al. (2023): LANL + temporal graph 기반 lateral movement
- 기존 IDS / GNN 기반 공격 탐지 연구

## 4. 데이터셋 (Dataset)
- 사용 데이터 : LANL Unfied
- 기간 : 
- 데이터 형태 : 
- 전처리 계획

## 5. 방법론(Methodology)
### 5.1 전체 파이프라인
1. LANL 이벤트 수집 및 전처리
2. TGN기반 모델 구성
3. Prediction head : link prediction/action classification
4. Neurosymbolic Rule 추가 및 적용
5. 학습 -> 평가 -> 실험 반복
### 5.2 모델 구성
- Base model : TGN
- Components : 
  - Memory Module
  - Message Function
  - Aggregator
  - Temporal Encoding
  - Node/Edge Embeddiing
  - Prediction Head
- Neurosymbolic Layer : 추후 작성

## 6.실험 계획()
## 7.기대결과
## 8. 리스크 및 대응 방안
## 9. 예상 한계 및 문제
LANL데이터셋은 이벤트에서 처음 보는 node가 자주 등장하는데, 이는 model로 하여금 기존 node feature을 이용한 예측을 어렵게 만들며 event feature로만 예측을 하게 만듬. 즉, TGN의 spatial한 장점을 살리지 못함.
