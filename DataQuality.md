# Data Quality {#DataQuality}


*Chapter leads: Martijn Schuemie, Vojtech Huser & Clair Blacketer* 

관찰형 의료 연구에서 사용되는 대부분의 데이터는 연구를 목적으로 수집되지 않는다. 예를 들어, 전자건강기록 (Electronic Health Records, EHRs)은 환자의 진료를 지원하는데 필요한 정보를 수집하기 위해, 청구 데이터는 비용 지불자에게 비용을 할당하기 위한 근거를 제공하기 위해 수집된다. 많은 이들이 이러한 데이터가 임상 연구에 적합한지 여부에 대해 의문을 가지고 있으며, @vanDerLei_1991 은 "데이터는 수집된 목적으로만 사용되어야 한다 (Data shall be used only for the purpose for which they were collected.)"고 주장하였다. 문제는 데이터가 우리가 원하는 연구를 위해 수집되지 않았기 때문에, 충분한 품질을 보자할 수 없다는 것이다. 데이터의 품질이 낮으면 (garbage in), 그 데이터를 사용한 연구 결과의 품질도 낮을 수 밖에 없다 (garbage out). 따라서 관찰형 의료 연구에 있어서 데이터 품질을 평가하는 것은 중요하며, 다음의 질문에 대한 답을 목표로 한다:

> 연구 목적에 적합한 데이터인가 (Are the data of sufficient quality for our research purposes) ?

우리는 데이터 품질 (DQ)을 다음과 같이 정의할 수 있다 [@roebuck_2012]: \index{data quality}

> 데이터를 특정 목적에 적합하게 만드는 완전성(Completeness), 유효성(Validity), 일관성(Consistency), 적시성(Timeliness), 정확성(Accuracy)의 상태 (The state of completeness, validity, consistency, timeliness and accuracy that makes data appropriate for a specific use).

주목할만한 것은 우리의 데이터가 완벽하지는 않지만, 목적에 충분히 적합할 수 있는 있다는 것이다. 

DQ를 직접적으로 관찰할 수는 없지만, 이를 평가하기 위한 방법론이 개발되어 왔다. DQ평가는 2가지 유형으로 구분될 수 있다 [@weiskopf_2013]: 보편적인 DQ를 확인하기 위한 평가, 특정 연구의 맥락에서 DQ를 확인하기 위한 평가.

본 장에서 우리는 먼저, DQ 문제가 발생할 수 있는 원인에 대해 검토하고, 보편적인 DQ와 연구 목적의 DQ 평가 이론에 대해 논의 후, OHDSI 툴들을 사용하여 이러한 평가를 어떻게 수행하는지 단계별로 설명하고자 한다. 

## 데이터 품질 문제에 대한 원인 (Sources of Data Quality Problems)

\@ref(EvidenceQuality)장에서 언급한 바와 같이, 의사들이 본인의 생각을 기록할 때 데이터 품질과 관련된 많은 위험요소들이 발생한다. @dasu_2003 은 각 단계에 구성된 DQ를 제시하면서, 데이터의 수명주기에 따라  DQ를 구분하였다. 그들은 이를 DQ 연속체 (DQ continuum)라 하였다:

1. **데이터 수집 및 통합 (Data gathering and integration)**. 발생 가능한 문제로 잘못된 수기 입력, 편향 (예. upcoding in claims), EHR에서 잘못된 테이블과의 결합, 누락값을 기본값으로 대체하는 것 등이 있다. 
2. **데이터 저장 및 지식 공유 (Data storage and knowledge sharing)**. 데이터 모델에 대한 문서화 부족, 메타 데이터의 부족이 잠재적인 문제로 여겨진다. 
3. **데이터 분석 (Data analysis)**. 잘못된 데이터 변환, 부정확한 데이터 해석, 그리고 부적절한 방법론 사용 등의 문제가 포함될 수 있다.  
4. **Data publishing**. 후속 사용을 위해 데이터를 게시하는 경우 (When publishing data for downstream use).

우리가 사용하는 데이터는 종종 이미 수집되고 통합되어 있기 때문에, 1단계에서 개선할 수 있는 것은 거의 없다. 이 단계에서 생성된 DQ를 확인할 수 있는 방법은 다음 절에서 논의될 것이다. 

유사하게, 특정 형식으로 데이터를 받기 때문에 2단꼐에 대한 영향력도 거의 없다. 하지만 OHDSI에서 우리는 우리의 관찰 데이터를 공통데이터모델 (Common Data Model, CDM)로 변환하고, 이 프로세스에 대한 소유권을 갖는다. 몇몇은 이러한 특정 단계가 DQ를 저하시킬 것이라 우려를 표한다. 하지만 우리는 이 프로세스를 통제하기 때문에, 이후 15.2.2절\@ref(etlUnitTests)에서 논의하는 것과 같이 DQ를 보호하기 위한 엄격한 안전장치를 구축할 수 있다. 여러 연구[@defalco_2013;@makadia_2014;@matcho_2014;@voss_2015;@voss_2015b;@hripcsak_2018]에 따르면 이 과정이 제대로 실행된다면 CDM으로 변환했을 때 오류가 거의 발생하지 않는 것으로 나타났다. 실제로, 대규모 공동체에 의해 공유되는 잘 문서화된 데이터 모델은 명백하고 명확한 방법으로 데이터 저장을 용이하게 한다. 

3단계 (데이터 분석) 또한 우리의 통제 아래에 있다. OHDSI에서 우리는 이 단계의 품질 이슈에 대해 DQ 용어를 사용하지 않고 각각 16장\@ref(ClinicalValidity), 17장\@ref(SoftwareValidity), 그리고 18장\@ref(MethodValidity)에서 다루는 *clinical validity*, *software validity* 그리고*method validity*라는 용어를 사용한다. 

## 보편적인 데이터 품질 (Data Quality in General)

우리는 우리의 데이터가 관찰형 연구의 보편적인 목적에 적합한지 여부에 대해 의문을 가질 수 있다. @kahn_harmonized_2016 은 보편적인 DQ가 3가지 구성요소로 구성되어있다고 정의하였다: 

1. **적합성(Conformance)**: 데이터 값이 지정된 표준과 형식을 준수하는가? 3가지 하위 유형으로 식별됨:
   - **Value**: 기록된 데이터의 요소가 지정된 형식과 일치하는가? 예. 모든 Provider의 의료 전문분야는 유효한 전문 분야인가? 
   - **Relational**: 기록된 데이터가 지정된 관계적 제약(relational constraints)과 일치하는가? 예. DRUG_EXPOSURE 테이블의 PROVIDER_ID가 PROVIDER 테이블의 해당 레코드를 가지고 있는가? 
   - **Computation**: 데이터에 대한 계산이 의도한 결과를 산출하는가? 예. 키와 몸무게에서 계산된 BMI와 데이터에 기록된 BMI가 일치하는가? 
2. **완전성 (Completeness)**: 특정 변수가 존재하는지 여부(예. 진료실에서 측정된 체중이 기록되어 있는가?)와 모든 변수의 값이 기록되어 있는지 여부(예. 모든 사람이 성별에 관련된 데이터를 가지고 있는가?)를 나타냄 
3. **Plausibility**: 데이터의 값을 믿을 수 있는가? 3가지 하위 유형으로 식별됨:
    - **Uniqueness**: 예. 각각의 PERSON_ID는 PERSON 테이블에서 한번만 발생하는가? 
    - **Atemporal**: 값, 분표 또는 밀도가 예상 값과 일치하는가? 예. 데이터에 의해 암시된 당뇨병의 유행이 실제 알려진 유행과 일치하는가? 
    - **Temporal**: 값의 변화가 예상과 일치하는가? 예. 예방접종 순서는 권고사항과 일치하는가? 
    
    \index{data quality!conformance} \index{data quality!completeness} \index{data quality!plausibility}

각각의 구성요소는 두 가지 방법으로 평가될 수 있다:

* **Verification** 외부 참조에 의존하지 않고, 모델, 메타데이터의 데이터 제약, 시스템 추정, 그리고 Local 지식에 주력한다. Verification의 주요 특징은 Local 환경 내의 자원을 사용하여 예상되는 값과 분포를 설명하는 능력이다. 
* **Validation** 관련된 외부 benchmarks와 관련된 데이터 값과의 일치에 주력한다. 외부 benchmark로 가능한 원천으로는 여러 데이터 사이트에 걸쳐 결과를 결합하는 것이 있다. 

\index{data quality!verification} \index{data quality!validation}

### 데이터 품질 checks (Data Qualityintegrate Checks)

\index{ACHILLES} \index{data quality!checks}

Kahn은 데이터가 주어진 요구조건을 준수하는지 테스트하기 위해  *데이터 품질 check (data quality check)* (때로는 *데이터 품질 rule (data quality rule)*이라고도 함)라는 용어를 도입하였다 (예. 잘못된 출생 연도 또는 사망 사건의 누락으로 인해 141세라는 환자의 신뢰할 수 없는 연령 표시). 우리는 자동화된 DQ 툴들을 만들어 소프트웨어에서 이러한 check들을 수행한다. 이러한 툴 중 하나가 [ACHILLES](https://github.com/OHDSI/Achilles) (Automated Characterization of Health Information at Large-scale Longitudinal Evidence Systems, ACHILLES)이다. [@huser_methods_2018] ACHILLES는 CDM에 부합하는 데이터베이스의 특성과 시각화를 제공하는 소프트웨어 툴이다.  데이터베이스 네트워크에서 DQ를 평가하는데 사용할 수 있다 [@huser_multisite_2016]. ACHILLES는 독립형 툴로서 사용 가능하며, "데이터 소스(Data Sources)" 기능으로 ATLAS에서도 통합되어 있다. \index{data quality!data quality check} \index{ACHILLES}

ACHILLES는 각 분석마다 분석 ID와 간단한 설명을 지닌 170개 이상의 데이터 특성 분석을 사전 계산한다. 이와 관련된 두 가지 예시는 다음과 같다. "715: DRUG_CONCEPT에 의한 DAYS_SUPPLY의 분포" and "506: 성별에 따른 사망 연령 분포". 이러한 분석 결과는 데이터베이스에 저장되며, 웹 뷰어(web viewer) 또는 ATLAS에서 엑세스 할 수 있다. 

\index{Data Quality Dashboard}

공동체에서 DQ 평가를 위해 만든 또 다른 툴로 [Data Quality Dashboard (DQD)](https://github.com/OHDSI/DataQualityDashboard)가 있다. ACHILLES가 특성화 분석을 실행하여 CDM 인스턴스(instance)에 대한 전반적인 시각적 이해를 제공한다면, DQD는 테이블 별, 필드 별로 주어진 규격에 적합하지 않는 CDM의 레코드 수를 정량화한다. 전체적으로, 1500건 이상의 checks가 수행되고, 각각은 Kahn의 프레임워크로 구성된다. 각 check의 결과는 임계값과 비교되며, FAIL은 해당 값을 초과하는 위반 뱅의 백분율로 간주된다. 표 15.1\@ref(tab:dqdExamples)은 checks의 예시를 보여준다. 

표: (\#tab:dqdExamples) Data Quality Dashboard에서 데이터 품질 rules 예시.

| 위반 행의 분율  | Check 설명 | 임계값 | 상태 |
|:-------- |:------------------------------------ |:------ |:---- |
| 0.34| VISIT_OCCURRENCE의 provider_id가 설명서를 기반으로 예측되는 데이터 형식인지를 나타내는 yes 또는 no 값. | 0.05 | FAIL|
| 0.99| MEASUREMENT 테이블의 measurement_source_value 필드에서 0으로 매핑되어 있는 소스 데이터의 수와 백분율. | 0.30 | FAIL|
| 0.09| DRUG_ERA 테이블의 drug_concept_id 필드에서 성분 클래스에 적합하지 않은 값을 가진 레코드 수와 백분율. | 0.10 | PASS|
| 0.02| DRUG_EXPOSURE 테이블에서 verbatim_end_date 필드에 drug_exposure_start_date 이전에 발생한 값이 있는 레코드 수와 백분율.|0.05|PASS|
| 0.00| PROCEDURE_OCCURRENCE 테이블의 procedure_occurrence_id 필드에 중복되는 값이 있는 레코드 수와 백분율. | 0.00 | PASS|

툴에서, DQ checks는 테이블, 필드 및 concept 레벨의 checks 중 하나에 속하며 여러 가지 방식으로 구성된다. 테이블 checks는 모든 필수 테이블이 존재하는지 판별하는 등 CDM 내에서 상위 레벨로 수행되는 check이다. 필드 레벨의 checks는 모든 테이블의 모든 필드가 CDM 규격에 적합한지 평가하는 방법으로 수행된다. 이는 모든 기본키가 실제로 고유한지 확인하는 것과 모든 표준 concept 필드가 많은 다른 것들 중 적절한 도메인의 concept_ID를 포함하는지 확인하는 것이 포함된다. Concept 레벨의 검사는 개별적인 concept_id를 조사하기 위해 조금 더 깊이 들어간다. 이 중 상당수는 성별에 따른 concept이 부정확한 성별(예. 여성 환자의 전립선 암)에 속하지 않도록 하는 것과 같이 Kahn의 프레임워크 중 타당성(Plausibility) 카테고리에 속한다. 

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant">ACHILLES와 DQD는 CDM 데이터에서 실행된다. 이렇게 식별된 DQ 이슈는 cDM으로의 변환때문일 수 있지만, 소스 데이터에서 이미 존재하는 DQ 문제를 반영할 수도 있다. 만약 변환이 잘못되었을 경우, 문제를 해결하는 것은 우리의 통제 아래에 있지만, 기본 데이터가 잘못되었을 경우, 유일한 조치 행동은 위반된 기록을 삭제하는 것이다.
</div>\EndKnitrBlock{rmdimportant}

### ETL Unit Tests {#etlUnitTests}

\index{ETL!unit tests}

상위 레벨의 데이터 품질 checks뿐만 아니라, 개별 수준의 데이터 품질 checks도 수행되어야 한다. 데이터가 CDM으로 변환되는 ETL(추출-변환-적재, Extract-Transform-Load) 프로세스는 종종 상당히 복잡하고, 이러한 복잡성으로 인해 실수를 눈치 채지 못할 위험이 있다. 더욱이, 시간이 지남에 따라 원본 데이터 모델이 변경되거나, CDM 버전이 업데이트 되면, ETL 프로세스를 수정할 필요가 있다. ETL과 같이 복잡한 프로세스의 변경은 의도하지 않은 결과를 초래할 수 있고, ETL의 모든 측면을 재고하고 검토해야 한다. 

ETL이 해야하는 것을 정확히 하고, 계속하기 위해서 일련의 unit tests를 구성하는 것이 매우 권장된다.  Unit test란 하나의 측면을 자동으로 확인하는 작은 코드 조각이다. 6장에서\@ref(ExtractTransformLoad) 설명한 Rabbit-in-a-Hat 툴로 이러한 Unit test를 보다 쉽게 작성할 수 있는 Unit test 프레임워크를 만들 수 있다. 이 프레임워크는 ETL에서 소스 데이터베이스와 타켓 CDM 버전을 위해 특별히 만들어진 R 함수의 모음이다. 이러한 함수 중 일부는 소스데이터 스키마를 준수하는 가짜 데이터 항목을 만들기 위한 것이며, 다른 기능은 CDM 형식으로 데이터에 대한 예상값을 정하는데 사용될 수 있다. Unit test에대한 예시는 다음과 같다:



```r
source("Framework.R")
declareTest(101, "Person gender mappings")
add_enrollment(member_id = "M000000102", gender_of_member = "male")
add_enrollment(member_id = "M000000103", gender_of_member = "female")
expect_person(PERSON_ID = 102, GENDER_CONCEPT_ID = 8507
expect_person(PERSON_ID = 103, GENDER_CONCEPT_ID = 8532)
```

예제에서, Rabbit-in-a-Hat에 의해 생성된 프레임워크는 나머지 코드에서 사용되는 함수를 불러오는 코드이다. 그리고 나서 우리는 성별 매핑(Person gender mappings)에 대한 테스트를 시작할 것이라 선언하였다. 소스 스키마는 ENROLLMENT 테이블을 가지고 있고, 우리는 Rabbit-in-a-Hat에서 생성된 add_enrollment 함수를 사용하여 MEMBER_ID와 GENDER_OF_MEMBER 필드에 대해 서로 다른 값을 지닌 두 개의 항목을 만들었다. 마지막으로, ETL 이후 PERSON 테이블에서 다양한 예상 값을 지닌 두 개의 항목이 존재해야 한다는 것을 명시한다. 

ENROLLMENT 테이블에는 많은 다른 필드가 있지만, 이 테스트의 맥락에서는 이러한 다른 필드가 어떤 값을 가지는지는 중요하지 않다. 하지만 이러한 값을 비워두면 (예. 생년월일), 레코드를 삭제하거나 오류를 발생시키는 ETL의 원인이 될 수 있다. 테스트 코드를 읽기 쉽게 유지하면서 이러한 문제를 해결하기 위해서, add_enrollment 함수는 사용자가 명확하게 지정하지 않은 필드의 값에 기본값(White Rabbit 스캔 보고서에서 관찰된 가장 일반적인 값)을 할당한다. 

ETL의 모든 다른 논리에 대해 유사한 Unit test가 만들어 질 수 있으며, 일반적으로 수백 개의 시험이 발생한다. 테스트를 정의하는 것이 끝나면, 가짜 소스 데이터를 생성하기 위한 것과 ETL된 데이터를 test에 생성할 수 있는 두 개의 SQL 문을 생성하기 위해 프레임워크를 사용할 수 있다. 



```r
insertSql <- generateInsertSql(databaseSchema = "source_schema")
testSql <- generateTestSql(databaseSchema = "cdm_test_schema")
```

전반적인 프로세스는 그림 \@ref(fig:testFramework)에 묘사된 것과 같다.

<div class="figure" style="text-align: center">
<img src="images/DataQuality/testFramework.png" alt="Rabbit-in-a-Hat 테스팅 프레임워크를 사용한 추출변환적재(ETL) Unit testing 과정." width="90%" />
<p class="caption">(\#fig:testFramework)Rabbit-in-a-Hat 테스팅 프레임워크를 사용한 추출변환적재(ETL) Unit testing 과정.</p>
</div>

테스틑 SQL은 표\@ref(tab:exampleTestResults)와 같은 테이블을 반환한다. 이 표에서는 우리가 앞서 정의한 두 가지 테스트를 통과하는 것을 알 수 있다.  

표: (\#tab:exampleTestResults) ETL unit test 결과 예시.

| ID    | 설 명          | 상 태 |
|:-----:|:---------------------- |:------:|
| 101   | 성별 매핑(Person gender mappings) | PASS   |
| 101   | 성별 매핑(Person gender mappings) | PASS   |

이 unit test의 강점은 ETL 프로세스가 변경될 때마다 쉽게 재실행할 수 있다는 것이다. 

## 연구 목적 checks (Study-Specific Checks)

\index{data quality!study-specific checks}

본 장에서는 지금까지 보편적인 DQ 검사에 초점을 맞췄다. 이러한 검사들은 데이터가 연구에 사용되기 이전에 실행되어야 한다. 이러한 검사는 연구 문제와 무관하게 수행되어야 하므로 연구 목적의 DQ 평가를 수행할 것을 권한다. 

이러한 평가 중 일부는 특별히 연구와 관련된 DQ rule의 형태를 취할 수 있다. 예를 들어, 관심 노출에 대한 레코드의 최소 90%가 노출 기간을 명시한다는 새로운 rule의 도입을 원할 수도 있다. 

표준 평가는 ACHILLES에서 연구에 가장 관련 있는 concept, 예를 들어 cohort definition으로 지정된 concept을 검토하는 것이다. 관찰된 코드와 동일한, 시간에 따른 갑작스러운 변화는 DQ 문제에 힌트가 될 수 있다.  몇몇 예시들은 이 장의 뒷부분에서recommend 설명하고 있다. 

또다른 평가는 연구를 위해 설정된 cohort definition을 사용해 생성된 코호트 결과에 대한 유병률과 시간에 따른 유병률의 변화를 검토하고 이것이 외부 임상 지식에 기반한 예상값과 일치하는지 확인하는 것이다. 예를 들어, 신약의 노출은 시장에 소개되기 전에는 없어야 하고, 도입 이후에 시간이 지남에 따라 증가할 가능성이 있다. 유사하게 결과에 대한 유병률은 모집단에서 질환의 유병률에 대해 알려진 것과 일치해야 한다. 만약 연구가 데이터베이스의 네트워크에서 실행한다면, 우리는 데이터베이스 간의 코호트 유병률을 비교할 수 있다. 한 데이터베이스에서 높은 유병률을 보이지만, 다른 데이터베이스에서는 누락된 경우, DQ 문제가 발생할 수 있다. 이러한 평가는 16장\@ref(ClinicalValidity)에서 논의한 바와 같이, *clinical validity*의 개념과 중복된다는 것을 유의해야 한다. 몇몇 데이터베이스에서는 cohort definition이 실제로 관심 있는 건강 상태를 담아내지 못하거나 다른 환자 모집단을 기록하는 데이터베이스에 따라 건강상태가 다를 수 있기 때문에 DQ 문제가 아닌 예기치 못한 유병률을 발견할 수도 있다.


### Checking Mappings

우리가 통제할 수 있는 오류의 원인 중 한가지는 소스 코드를 표준 concept에 매핑하는 것이다. Vocabylary에서 매핑은 꼼꼼하게 만들어졌고, 공동체 구성원에 의해 알려진 매핑 오류는 Vocabulary 이슈란[^vocabIssueTrackerUrl]에 보고되고 다음 업데이트에서 수정된다. 그럼에도 불구하고 모든 매핑을 직접 확인하는 것은 불가능하고 오류는 여전히 존재한다. 연구를 수행할 때, 연구와 가장 관련이 있는 concept에 대한 매핑을 검토하는 것이 좋다. 다행히도, CDM에서는 표준화 concept 뿐 아니라, 소스 코드도 저장하기 때문에 매우 쉽게 할 수 있다. 연구에 사용된 concept에 매핑 된 소스 코드뿐만 아니라 그렇지 않은 소스 코드도 검토할 수 있다.

[^vocabIssueTrackerUrl]: https://github.com/OHDSI/Vocabulary-v5.0/issues

소스 코드를 검토하는 한 가지 방법은 [MethodEvaluation](https://ohdsi.github.io/MethodEvaluation/) R 패키지의 `checkCohortSourceCodes` 함수를 사용하는 것이다. 이 함수는 ATLAS에서 생성된 cohort definition을 input으로 사용하고 cohort definition에서 사용된 각 concept 세트에 대해 concept과 매핑되는 소스코드를 확인한다. 또한 특정 소스 코드와 관련된 시간적 문제를 확인하는데 도움이 되도록 시간에 따른 코드의 유병률을 계산한다. 그림 \@ref(fig:sourceCodes) 예시 결과는  "우울증 (Depression disorder)"이라 불리는 concpet 세트의 분석을 보여준다. 관심 분야의 데이터베이스에서 이 concept 세트의 가장 보편적인 concept은  [440383](http://athena.ohdsi.org/search-terms/terms/440383) (우울증; Depressive disorder)이다. 데이터베이스에서 세 가지 소스 코드가 이 concept으로 매핑된다 : ICD-9 코드의 3.11, ICD-10 코드의 F32.8과 F32.89. 왼쪽에서 그 개념이 처음에는 시간이 지남에 따라 점진적으로 증가하지만, 그 후에 급격히 감소하는 것을 볼 수 있다. 개별 코드를 살펴보면, 이러한 하락은 하락 시점에 ICD-9 코드의 사용이 중단되는 것으로 설명될 수 있다는 것을 알 수 있다. 이것이 ICD-10 코드가 사용되기 시작한 것과 같은 시간임에도 불구하고, ICD-10 코드의 결합된 유병률은 ICD-9 코드의 유병률보다 훨씬 적다. 이 구체적인 예시는 ICD-10 코드 F32.9 ("주요 우울 장애, 단일 에피소드, 불특정")도 이 concept으로 매핑 되었어야 했기 때문이다. 이 문제는 Vocabulary에서 해결되었다.

<div class="figure" style="text-align: center">
<img src="images/DataQuality/sourceCodes.png" alt="checkCohortSourceCodes 기능의 output 예시. " width="100%" />
<p class="caption">(\#fig:sourceCodes)checkCohortSourceCodes 기능의 output 예시. </p>
</div>

앞의 예시는 매핑되지 않은 소스 코드를 발견하는 것을 묘사한 것으로, 일반적으로 누락된 매핑을 식별하는 것이 존재하는 매핑을 확인하는 것보다 더 어렵다. 이는 어떤 소스 코드가 매핑되어야 하지만 매핑되지 않았는지 알아야 한다. 이를 평가하는 반-자동화된 방법은 [MethodEvaluation](https://ohdsi.github.io/MethodEvaluation/) R 패키지의 `findOrphanSourceCodes` 함수를 사용하는 것이다. 이 함수는 간단한 텍스트 검색을 통해 소스 코드에 대한 Vocabulary를 검색할 수 있게 하고, 이 소스 코드가 특정 concept이나 그 concept의 하위 concept 중 하나와 매핑되는지 여부를 확인한다. 소스 코드의 결과는 현재 CDM 데이터베이스에 나타나는 코드로만 제한된다. 예를 들어, "괴저 장애 (Gangrenous disorder)"  ([439928](http://athena.ohdsi.org/search-terms/terms/439928)) 와 모든 하위 concept들은 모든 괴저 발생을 찾기 위해 사용되었다. 이것이 실제로 괴저를 나타내는 모든 소스 코드를 포함하는지 여부를 평가하기 위해, 소스 코드를 식별하기 위한 CONCEPT 테이블과 SOURCE_TO_CONCEPT_MAP 테이블의 설명을 검색하는데 몇 가지 용어(예. "괴저 (gangrene)")가 사용되었다. 자동 검색은 데이터에 나타나는 각 괴저 코드가 "괴저 장애 (Gangrenous disorder)"라는 concept에 직접 또는 간접적으로 매핑 되었는지 여부를 평가하기 위해 사용된다. 이러한 평가의 결과는 그림 \@ref(fig:missingMapping)와 같으며, ICD-10 코드 J85.0 ("폐의 괴저 및 괴사"; Gangrene and necrosis of lung)은 "괴저 장애 (Gangrenous disorder)의 하위 컨셉이 아닌 컨셉 [4324261](http://athena.ohdsi.org/search-terms/terms/4324261)("폐 괴사"; Pulmonary necrosis)에만 매핑 되었다.  \index{orphan codes}

<div class="figure" style="text-align: center">
<img src="images/DataQuality/missingMapping.png" alt="orphan 소스코드 예시. " width="70%" />
<p class="caption">(\#fig:missingMapping)orphan 소스코드 예시. </p>
</div>

## ACHILLES 실습 (ACHILLES in Practice) {#achillesInPractice}

여기서는 CDM 형식의 데이터베이스에 대해 ACHILLES를 실행하는 방법을 보여준다.  

먼저, R에서 서버를 연결하는 방법에 대해 설명할 필요가 있다. ACHILLES는 `createConnectionDetails`라는 함수를 제공하는 [DatabaseConnector](https://ohdsi.github.io/DatabaseConnector/) 패키지를 사용한다. . 다양한 데이터베이스 관리 시스템 (Database management systems, DBMS)에 필요한 특정 설정을 `?createConnectionDetails`을 입력하여 확인할 수 있다. 예를 들어, 다음 코드를 이용하여 PostgreSQl과 연결할 수 있다. :



```r
library(Achilles)
connDetails <- createConnectionDetails(dbms = "postgresql",
                                       server = "localhost/ohdsi",
                                       user = "joe",
                                       password = "supersecret")

cdmDbSchema <- "my_cdm_data"
cdmVersion <- "5.3.0"
```

마지막 두 줄은 CDM 버전뿐 만 아니라 `cdmDbSchema` 변수를 정의한다. 이를 사용하여 CDM 형식의 데이터가 어디에 있는지, 어떤 버전의 CDM이 사용되었는지 R에 입력한다. Microsoft SQL Server의 경우, `cdmDbSchema <- "my_cdm_data.dbo"`와 같이 데이터베이스와 스키마를 모두 지정해야 한다.

다음으로, ACHILLES를 실행한다:



```r
result <- achilles(connectionDetails,
                   cdmDatabaseSchema = cdmDbSchema,
                   resultsDatabaseSchema = cdmDbSchema,
                   sourceName = "My database",
                   cdmVersion = cdmVersion)
```

이 함수는`resultsDatabaseSchema`에 여러 테이블을 생성하며, 여기에서는 CDM 데이터와 동일한 데이터베이스 스키마로 설정하였다. 

ATLAS를 ACHILLES 결과 데이터베이스로 지정하거나 ACHILLES 결과를 JSON 파일로 내보내서 ACHILLES 데이터베이스의 특징을 볼 수 있다.:



```r
exportToJson(connectionDetails,
             cdmDatabaseSchema = cdmDatabaseSchema,
             resultsDatabaseSchema = cdmDatabaseSchema,
             outputPath = "achillesOut")
```

JSON 파일은 achillesOut 하위 폴더에 작성되고, 결과 확인을 위해 AchillesWeb 웹 어플리케이션과 함께 사용할 수 있다. 예를 들어, 그림 \@ref(fig:achillesDataDensity) ACHILLES 데이터 밀도 plot을 보여준다. 이 plot은 2005년에 시작된 대량의 데이터를 보여준다. 하지만 1961년경에 몇 개의 레코드가 있는 것으로 나타나며, 이는 데이터에 오류가 있는 것일 수도 있다. 

<div class="figure" style="text-align: center">
<img src="images/DataQuality/achillesDataDensity.png" alt="ACHILLES 웹 뷰어의 데이터 밀도 그림." width="100%" />
<p class="caption">(\#fig:achillesDataDensity)ACHILLES 웹 뷰어의 데이터 밀도 그림.</p>
</div>

또 다른 예시로는 그림 \@ref(fig:achillesCodeChange)로, 당뇨병 진단 코드의 유병률에 급격한 변화를 보여주고 있다. 이러한 변화는 특정 국가의 급여 규정의 변경과 일치하여 더 많은 진단이 제공되었기 때문이지 실제로 기본 인구의 유병률이 증가하지는 않았다. 

<div class="figure" style="text-align: center">
<img src="images/DataQuality/achillesCodeChange.png" alt="ACHILLES 웹 뷰어에서 코딩된 월별 당뇨병 발생률." width="100%" />
<p class="caption">(\#fig:achillesCodeChange)ACHILLES 웹 뷰어에서 코딩된 월별 당뇨병 발생률.</p>
</div>
## Data Quality Dashboard 실습 (Data Quality Dashboard in Practice) {#dqdInPractice}

여기에서는 CDM 형식의 데이터베이스에서 Data Quality Dashboard를 실행하는 방법을 보여준다.15.4절\@ref(achillesInPractice)에서 설명한 CDM conncection에 대해 더 많은 checks를 수행한다. 현재 DQD는 CDM v5.3.1만 지원하기 때문에 실행 전 데이터베이스가 올바른 버전인지 확인이 필요하다. ACHILLES와 마찬가지로 `cdmDbSchema`를 작성하여 데이터를 찾을 위치를 R에 입력해야 한다. 


```r
cdmDbSchema <- "my_cdm_data.dbo"
```

다음으로, Dashboard를 실행한다...


```r
DataQualityDashboard::executeDqChecks(connectionDetails = connectionDetails, 
                                      cdmDatabaseSchema = cdmDbSchema, 
                                      resultsDatabaseSchema = cdmDbSchema,
                                      cdmSourceName = "My database",
                                      outputFolder = "My output")
```

위의 함수는 지정된 스키마에서 사용 가능한 모든 데이터 품질 checks를 실행한다. 그런 다음 CDM과 동일한 스키마로 설정한 `resultsDatabaseSchema`에 테이블을 작성한다. 이 테이블은 CDM 테이블, CDM 필드, 검사 명, 설명, Kahn의 카테고리와 하위 카테고리, 위반 행의 수, 임계 값 레벨 그리고 검사의 통과여부 등 각 check의 실행에 대한 모든 정보가 포함된다. 이 함수는 테이블뿐만 아니라 `outputFolder`로 JSON 파일을 작성할 위치를 지정한다. JSON 파일을 사용해서 웹 뷰어를 시작해 결과를 확인할 수 있다. 


```r
viewDqDashboard(jsonPath)
```

`jsonPath` 변수는 위의 `executeDqChecks` 함수가 호출될 때, 지정된 `outputFolder`에 위치한 Dashboard의 결과가 포함된 JSON 파일의 경로여야 한다. 

처음 Dashoboaard를 열면 그림 \@ref(fig:dqdOverview)과 같이 개요 테이블이 표시된다. 여기에는 내용별 Kahn의 카테고리에서 실행된 총 검사의 수, 각 검사의 PASS 수와 백분율 및 전체 PASS 비율이 표시된다.  

<div class="figure" style="text-align: center">
<img src="images/DataQuality/dqdOverview.png" alt="Data Quality Dashboard 에서의 데이터 품질 점검 개요." width="100%" />
<p class="caption">(\#fig:dqdOverview)Data Quality Dashboard 에서의 데이터 품질 점검 개요.</p>
</div>

왼쪽 메뉴에서 *Results*를 클릭하면 실행된 각 검사에 대한 상세 결과 페이지로 이동한다(그림 \@ref(fig:dqdResults)). 예시의 테이블은 개별적인 CDM 테이블의 완전성을 확인하거나 CDM에서 특정 테이블에 최소 1개 이상의 레코드를 가진 인원 수 및 백분율을 확인하기 위한 검사에 대한 것이다. 이 경우 나열된 5개의 테이블이 Dashboarc에 Fail로 나타났으며 모두 비어있다. ![](images/DataQuality/plusIcon.png) 아이콘을 클릭하면 나열된 결과를 생성하기 위해 데이터에서 실행된 정확한 쿼리를 보여주는 창이 열린다. 이를 통해 Dashboard에서 Fail로 간주한 행을 쉽게 식별할 수 있다. 

<div class="figure" style="text-align: center">
<img src="images/DataQuality/dqdResults.png" alt="Drilldown into Data Quality Checks in the Data Quality Dashboard 에서의 데이터 품질 구체적 관찰." width="100%" />
<p class="caption">(\#fig:dqdResults)Drilldown into Data Quality Checks in the Data Quality Dashboard 에서의 데이터 품질 구체적 관찰.</p>
</div>

## 연구 목적 checks 실습 (Study-Specific Checks in Practice)

다음으로, Appendix \@ref(Angioedema)dp 제공된 혈관 부종 cohort definition에 대한 몇 가지 checks를 수행할 것이다. 15.4 절\@ref(achillesInPractice)에 설명된 것처럼 연결 세부사항이 설정되어 있고, cohort definition JSON과 cohort definition에 대한 SQL이 각각 "cohort.json"과 "cohort.sql" 파일에 저장되어있다고 가정한다. JSON 파일과 SQP 파일은 ATLAS cohort definition 기능의 내보내기 탭에서 얻을 수 있다.



```r
library(MethodEvaluation)
json <- readChar("cohort.json", file.info("cohort.json")$size)
sql <- readChar("cohort.sql", file.info("cohort.sql")$size)
checkCohortSourceCodes(connectionDetails,
                       cdmDatabaseSchema = cdmDbSchema,
                       cohortJson = json,
                       cohortSql = sql,
                       outputFile = "output.html")
```


그림 \@ref(fig:sourceCodesAngioedema)과 같이 웹 브라우저에서 output(출력) 파일을 열 수 있다. 여기서 혈관 부종 cohort definition에 "Inpatient or ER visit"과 "Angioedema" 두 가지 concept이 있는 것을 확인할 수 있다. 이 예제 데이터베이스에서, 방문은 ETL 중에 표준 concept과 매핑되었지만, Vocabulary에는 없는, "ER"과 "IP"라는 데이터베이스 특정 소스코드를 통해 발견되었다. 혈관 부종은 하나의 ICD-9 코드와 두 개의 ICD-10 코드를 통해 발견되었다.개별 코드에 대한 스파크 라인(spark-line)을 봤을 때, 두 가지 코딩 시스템 간의 컷 오버(cut-over) 시점을 명확하게 알 수 있지만 전체적인 concept에서는 불연속성이 없다. 

<div class="figure" style="text-align: center">
<img src="images/DataQuality/sourceCodesAngioedema.png" alt="Angioedema 코호트 정의에서 사용된 소스코드." width="100%" />
<p class="caption">(\#fig:sourceCodesAngioedema)Angioedema 코호트 정의에서 사용된 소스코드.</p>
</div>

다음으로, 표준 concept 코드에 매핑되지 않은 소스코드인 orphan 소스 코드를 검색할 수 있다. 표준 concept인 "혈관 부종 (Angioedema)"을 찾은 다음 "혈관 부종" 또는 그 이름이 일부 포함되어 있거나 동의어가 있는 concept과 코드를 찾는다:


```r
orphans <- findOrphanSourceCodes(connectionDetails,
                                 cdmDatabaseSchema = cdmDbSchema,
                                 conceptName = "Angioedema",
                                 conceptSynonyms = c("Angioneurotic edema",
                                                     "Giant hives",
                                                     "Giant urticaria",
                                                     "Periodic edema"))
View(orphans)
```
|code              |설명                                                            |vocabularyId | overallCount|
|:-----------------|:----------------------------------------------------------------------|:------------|------------:|
|T78.3XXS          |Angioneurotic edema, sequela                                           |ICD10CM      |          508|
|10002425          |Angioedemas                                                            |MedDRA       |            0|
|148774            |Angioneurotic Edema of Larynx                                          |CIEL         |            0|
|402383003         |Idiopathic urticaria and/or angioedema                                 |SNOMED       |            0|
|232437009         |Angioneurotic edema of larynx                                          |SNOMED       |            0|
|10002472          |Angioneurotic edema, not elsewhere classified                          |MedDRA       |            0|

데이터에서 실제로 사용된 유일한 잠재적 orphan 코드는 "혈관신경성 부종, 후유증 (Angioneurotic edema, sequela)"이며, 이는 혈관 부종과 매핑 되어서는 안 된다. 따라서 이 분석에서는 누락된 코드가 발견되지 않았다.

## 요약

\BeginKnitrBlock{rmdsummary}<div class="rmdsummary">- 대부분의 관찰형 의료 데이터는 연구를 위해 수집되지 않는다. 

- 대부분의 관찰형 의료 데이터는 연구를 위해 수집되지 않는다. 데이터 품질은 데이터가 연구 목적에 적합한지를 확인하기 위해 평가되어야 한다. 

- 보편적인 연구 목적을 위해, 특정 연구의 맥락에서 비판적으로 데이터 품질을 평가해야 한다.

- 데이터 품질의 일부 측면은 Data Quality Dashboard의 예시와 같이 사전 정의된 많은 규칙을 통해 자동적으로 평가될 수 있다. 

- 특정 연구와 관련된 코드 매핑을 평가하기 위한 다른 툴들이 있다.
</div>\EndKnitrBlock{rmdsummary}

## Exercises

#### Prerequisites {-}

예제 실습을 위해 8.4.5 절\@ref(installR)에서 설명한 것과 같이 R, R-studio 및 Java가 설치되어 있다고 가정한다. 또한 [SqlRender](https://ohdsi.github.io/SqlRender/),  [DatabaseConnector](https://ohdsi.github.io/DatabaseConnector/),  [ACHILLES](https://github.com/OHDSI/Achilles) 및  [Eunomia](https://ohdsi.github.io/Eunomia/) 패키지가 필요하다. 아래의 코드를 사용하여 설치할 수 있다:



```r
install.packages(c("SqlRender", "DatabaseConnector", "devtools"))
devtools::install_github("ohdsi/Achilles")
devtools:install_github("ohdsi/DataQualityDashboard")
devtools::install_github("ohdsi/Eunomia", ref = "v1.0.0")
```

Eunomia 패키지는 로컬 R 세션 내에서 실행되는 CDM에서 시뮬레이션 된 데이터 세트를 제공한다. 자세한 접속정보는 아래를 활용하여 얻을 수 있다:



```r
connectionDetails <- Eunomia::getEunomiaConnectionDetails()
```

CDM 데이터베이스 스키마는 "main"이다.

\BeginKnitrBlock{exercise}<div class="exercise"><span class="exercise" id="exr:exerciseRunAchilles"><strong>(\#exr:exerciseRunAchilles) </strong></span>Eunomia 데이터베이스에 대해 ACHILLES를 실행하시오.
</div>\EndKnitrBlock{exercise}

\BeginKnitrBlock{exercise}<div class="exercise"><span class="exercise" id="exr:exerciseRunDQD"><strong>(\#exr:exerciseRunDQD) </strong></span>Eunomia 데이터베이스에 대해 DataQaulityDashbiard를 실행하시오.
</div>\EndKnitrBlock{exercise}

\BeginKnitrBlock{exercise}<div class="exercise"><span class="exercise" id="exr:exerciseViewDQD"><strong>(\#exr:exerciseViewDQD) </strong></span>DQD 검사 목록을 추출하시오.
</div>\EndKnitrBlock{exercise}

예제에 대한 정답은 Appendix E.10\@ref(DataQualityanswers)에서 확인 가능하다.
