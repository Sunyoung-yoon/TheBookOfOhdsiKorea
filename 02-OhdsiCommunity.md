# The OHDSI Community{#ohdsiCommunity}

## 분산연구망과 공통데이터모델{#drnCdm}

### 분산연구망 (Distributed Research Network)
의료 데이터는 데이터 구조, 형식의 이질성, 데이터의 질과 양 등 기술적인 어려움과 기관의 허락, 개인정보보호문제 등 법적 문제 그리고 타인에게 제공하는 데이터가 자신에게 불리하게 사용될지 모른다는 두려움 등 자신의 자료를 타인과 공유하고 싶지 않는 문제들이 있어 공유가 쉽지 않았다. 현재까지 대부분의 기관간 공동 연구는 극히 일부의 환자 데이터를 연구 주도 기관과 공유함으로써 진행하였는데, 한 번의 공동 연구를 위해 막대한 노력과 시간, 자금이 들어가는 현실적인 문제와 개인 정보의 공유라는 윤리적인 문제들이 있어 왔따. 

이런 제약을 극복하는 방법 중 하나가 분산연구망이다. 이는 의료데이터 유통의 제약 요인 극복을 위해 병원과 기업 등 수요자 간에 원본데이터의 공유 없이 분산된 형태로 데이터를 관리하면서 기관 내에서 분석한 결과만 필요 할 때 거래하는 방식이다. 

쉽게 설명하면, 각 병원의 환자정보를 표준화 및 익명화한 후 데이터를 병원 폐쇄망 안에 두고 사용자의 요청에 따라서 기관 안에서 분석코드/프로그램을 실행해 분석된 요약 집합정보(평균, 합, 표준편차, 오즈비, 위험도 등)만 수요자에게 회신하는 방식이라고 할 수 있다. 수요자는 폐쇄망 안에 있는 환자의 개별 정보를 보거나 취득할 수 없지만, 전체 데이터를 모아서 분석한 것과 동일한 분석 결과를 도출할 수 있다(그림 1). 여러 기관에서 자료를 통합하기 위해서는 공통데이터모델이 적용되어야 한다.

### 공통데이터모델 (Common Data Model)
앞서 설명했듯이 데이터 표준화는 연구자들이 연구에 사용할 데이터를 공통 형식으로 저장하여 협업 연구, 대규모 분석 및 정교한 도구 및 방법론 공유를 가능하게 하는 중요한 프로세스이다. 하지만 표준화 작업을 하기 위해서는 오랜 시간과 비용이 소요된다. 

이런 문제를 해결하기 위해 등장한 것이 공통데이터모델이다. 이는 여러 병원들의 데이터를 효율적으로 활용하기 위하여 정의한 표준화된 데이터 구조이다. 다기관 공동 연구 수행 시에 기관별로 서로 다른 데이터 구조로 인해 다양한 어려움이 따르는 것을 해결해 주는 방식으로 기관별로 상이한 데이터 구조와 의미를 동일한 하나의 구조와 의미를 갖도록 변환하는 벙법이라고 이해하면 쉽다. 

공통데이터모델을 따르기 위해서는 각 병원이나 기관이 기존의 데이터를 공통데이터모델로 변환하는 과정이 필요하다. 이를 ETL(Extract, Transform, Load) 이라 한다. 공통데이터모델은 기존의 한계점 등을 고려하여 지속적으로 업데이트 되고 있는 중이다. 

대표적인 공통데이터모델로는 비영리 국제컨소시엄인 오딧세이(Observational Health Data and Informatics, 이하 OHDSI)와 약물부작용 조사를 위한 미국 FDA의 센티넬 공통데이터모델(이하 Sentinel CDM), 미국 국내에서의 비교효과연구를 위한 피코르넷(The National Patient-Centered Clinical Outcomes Research Network, 이하 PCORnet) 등이 존재한다. 

이중 대표적인 공통데이터모델인 OHDSI를 살펴보자. OHDSI는 2008년에 미국정부의 지원으로 결성된 Observational Medical Outcomes Partnership(OMOP)으로부터 파생된 국제적 협의체이다. 초기에는 관찰연구 방법론과 데이터를 활용하기 위한 분석 도구 및 시각화 도구, 그리고, 각 기관마다 다른 진단, 처방 용어를 통일한 표준용어를 만들었다. 

OMOP은 2013년 정부의 지원이 예정대로 종료된 후, OMOP CDM과 표준 용어 정의 등 OHDSI로 이관되어 계속되고 있다. 특히 OMOP시절에는 약물부작용 조사 방법론에 초점을 맞추었지만, 이후 OHDSI로 이관한 이후에는 약물의 안전성, 비교효과연구, 경제성 분석, 의료의 질, 인공지능 기반의 환자 개별 위험도 예측 등 임상 빅데이터 분석으로 진화해 나가고 있다.

또 다른 CDM인 Sentinel Initiative는 미국 식품의약국(Food and Drug Administration, 이하 FDA)로부터 시작되었다. 의료 제품의 안전성 감시를 위한 국가적 전자시스템으로 Sentinel 시스템을 개발하였다. 이 시스템은 FDA 규제 제품을 사용하여 보고된 이상 반응을 추적하는 기존의 감시 기능을 보완하여 FDA가 이러한 제품의 안전성을 사전에 평가할 수 있도록 한다. 

Sentinel은 데이터 파트너가 기존 환경에서 전자 데이터에 대한 물리적 및 운영상의 제어를 유지하는 분산 데이터 접근 방식을 사용한다. 분산된 접근 방식은 Sentinel CDM으로 저장된다. 참여하는 데이터 파트너는 자신들이 보유한 데이터를 통일된 Sentinel CDM으로 변환하므로 하나의 동일한 분석 프로그램으로 여러 기관 결과를 동시에 분석할 수 있다. 개인정보보호를 위하여 분석 쿼리가 배포되고 검색 결과가 보안 포털을 통해 반환된다. 모든 데이터 파트너들 사이에서 합쳐진 데이터 집합을 Sentinel Distributed Database(SDD)라고 한다.

또 다른 CDM인 PCORnet은 The Patient-Centered Outcomes Research Institute(PCORI)에서 2013년에 설립한 프로젝트로서 환자 전자건강기록(Electronic health records, EHR)을 이용하여 비교효과연구(Comparative effectiveness research, CER)를 수행하기 위한 목적으로 시작되었다. 50개 주에 걸쳐 11개의 임상 데이터 연구 네트워크(Clinical data research networks, CDRNs)와 18개의 환자 참여 연구 네트워크(Patient-powered research networks, PPRNs)를 설립했다. PCORnet이 구축하고 있는 연구 플랫폼의 핵심은 환자 중심의 접근 방식(patient-centered approach)이며 데이터는 중추 역할을 한다. 


## 오딧세이 네트워크 {#OHDSINetwork}
오딧세이 네트워크 (The Observational Health Data Sciences and Informatics, OHDSI network)는 의약품의 적절한 사용에 대한 관찰 연구의 증진을 위해 시작된 OMOP (Observational Medical Outcomes Partnership) 프로젝트를 전신으로 하여 만들어진 국제 컨소시엄이다. 공통데이터모델 (CDM) 및 분산연구망 (Distributed Research Network)을 채택한 연구 네트워크 중 아시아, 미국 및 유럽 등 국제적 참여 및 활동이 이루어 지고 있는 컨소시엄은 OHDSI가 유일하다. OHDSI 네트워크에서는 OMOP-CDM을 채택하여, 이를 발전시켜 나가고 있으며 국제적 연구자들의 적극적인 참여, 협력 및 토론과 함께 데이터 시각화, 분석 등의 소프트웨어를 개발 및 제공하고 있다.

## 오딧세이의 역사 {#OHDSIHistory}

## 미션, 비젼, 가치 {#MissionVissionValues}

### 오딧세이 미션

참여 공동체의 상호협력 하에 의료 발전을 촉진하는 증거를 생성하는 능력을 부여한다.

> To improve health by empowering a community to collaboratively generate the evidence that promotes better health decisions and better care.


### 오딧세이 비젼

의료 빅데이터의 분석을 통해 세계에 건강과 질병에 대한 포괄적인 이해를 제공한다.

> A world in which observational research produces a comprehensive understanding of health and disease.

### 오딧세이 핵심 가치

* **혁신성 Innovation**: 우리는 적극적으로 의료 빅데이터 분석 및 연구에 대한 혁신적인 방법론과 접근법을 찾고 격려한다.

> Observational research is a field which will benefit greatly from disruptive thinking. We actively seek and encourage fresh methodological approaches in our work.

* **재현성 Reproducibility**: 우리는 보건 증진을 위하여 정확하고, 재현 가능하며, 잘 보정된 증거를 찾도록 노력한다.

> Accurate, reproducible, and well-calibrated evidence is necessary for health improvement.

* **공동체 정신 Community**: 우리는 모든 참여자들을 환영하며 동등하게 우리의 활동에 참여할 수 있도록 돕는다.

> Everyone is welcome to actively participate in OHDSI, whether you are a patient, a health professional, a researcher, or someone who simply believes in our cause.

* **개방성 Openness**: 우리는 의사 결정 과정의 투명성을 지향하며, 우리의 진보 및 우리가 생성한 방법론, 소프트웨어, 증거를 가능한 공개적으로 접근 가능하게 한다.

> We strive to make all our community’s proceeds open and publicly accessible, including the methods, tools and the evidence that we generate.

* **협력 정신 Collaboration**: 우리는 참여자들의 실제적 요구를 우선적으로 다루고, 그것을 위해 공동으로 노력한다.

> We work collectively to prioritize and address the real world needs of our community’s participants.

* **선행의 정신 Beneficence**: 우리는 고통 받는 환자를 비롯하여 참여자 및 참여기관의 권리를 보호하기 위해 노력한다.

> We seek to protect the rights of individuals and organizations within our community at all times.