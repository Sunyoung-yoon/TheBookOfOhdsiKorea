# Method Validity {#MethodValidity}

\index{method validity}

*Chapter lead: Martijn Schuemie*

이번 챕터에서 우리는 다음의 질문에 대해 답하려 했다. 

> 우리의 방법론은 연구 질문에 대해 답하기에 유효한가?

“방법”에는 연구 설계뿐만 아니라 데이터와 설계의 시행도 포함됩니다. 따라서, 방법의 타당성은 다소 포괄적입니다. 좋은 자료의 질, 임상적 타당성 및 software 타당성 없이 좋은 방법의 유효성을 논하는 것은 때때로 불가능합니다. 근거의 질적 측면은 이미 방법론적 타당성을 고려하기 전에 별도로 다루어져야 합니다. 

방법 타당성을 확립할 때 핵심 행동은 분석에서 중요한 가정이 충족되었는지 여부를 평가하는 것입니다. 예를 들어, 우리는 성향 점수 짝짓기가 두 집단을 비교할 수 있게 한다고 가정하지만, 이것이 사실인지 평가할 필요가 있습니다. 가능한 경우, 이러한 가정을 검증하기 위해 경험적 검정을 실시해야 한다. 예를 들어, 우리는 두 집단이 실제로 짝짓기 후에 넓은 범위의 특성에서 비교 가능하다는 것을 보여주기 위해 진단을 생성할 수 있습니다. OHDSI에서 우리의 분석이 수행 될 때마다 생성되고 평가되어야 하는 많은 표준화된 진단을 개발했습니다.

이 장에서는 인구 수준 추정에 사용되는 방법의 타당성에 초점을 맞출 것입니다. 먼저, 일부 연구 설계별 진단을 간략히 강조한 다음, 모든 모집단 수준의 추정 연구가 아닌 대부분의 진단에 적용 가능한 진단에 대해 논의할 것이다. 다음은 OHDSI 도구를 사용하여 이러한 진단을 수행하는 방법에 대한 단계별 설명입니다. 우리는 OHDSI Methods Benchmark와 OHDSI Methods Library에 대한 적용에 대해 검토하면서 이 장의 주제를 마무리합니다.

## Design-Specific Diagnostics

\index{study diagnostics}

각 연구 설계마다 해당 설계마다의 특정한 진단이 있습니다. 이러한 진단 중 많은 부분이 OHDSI Methods Library의 R 패키지로 구현이 되어있고, 쉽게 사용할 수 있습니다. 예를 들어, \@ref(studyOutputs)절에는 [OHDSI Methods Library](https://ohdsi.github.io/MethodsLibrary/) 패키지에서 생성된 광범위한 진단이 나열되어 있습니다:

- 코호트들의 초기 비교성을 평가하기 위한 **Propensity score distribution**.
- 모형에서 제외해야하는 잠재적 변수를 식별하기 위한 **Propensity model**.
- propensity score 보정을 통해 코호트들을 비교할 수 있는지 평가 (기저 공변량을 통해 측정)를 위한 **Covariate balance**.
- 다양한 분석 단계에서 몇 명의 피험자가 제외되었는지를 관찰하여, 초기 관심 집단에 대한 결과의 일반화 가능성을 알리기 위한 **Attrition**.
- 질문에 대답하기에 충분한 데이터가 있는지 평가하기 위한 **Power**.
- 일반적인 관찰 시작 시간을 평가하고 Cox 모델의 비례 가정이 충족되는지 여부를 위한 **Kaplan Meier curve**.

다른 연구 설계는 이러한 설계의 다른 가정을 검정하기 위해 다른 진단을 필요로 한다. 예를 들어, 자기-대조군 환자 시리즈 (SCCS) 설계의 경우 관찰의 종료가 결과와는 무관하다는 필수 가정을 확인할 수 있습니다. 이 가정은 심근 경색과 같이 심각하고 잠재적으로 치명적인 사건의 경우에서 종종 위반됩니다. 우리는 관찰 기간 종료의 시점이 중도 절단과 중도절단 되지 않은 시점을 보여주는 히스토그램인 그림 \@ref(fig:timeToObsEnd)에 표시된 plot을 생성하여 가정이 유지되는지의 여부를 평가할 수 있습니다. 데이터에서 관찰 기간이 데이터 수집의 종료 날짜 (전체 데이터베이스에서 관찰이 중단된 날짜, 예를 들어, 추출 날짜 또는 연구 종료 날짜)를 중도절단 되지 않은 것으로 간주하고, 다른 모든 관찰을 중도절단한 것으로 간주한다. 그림 \@ref(fig:timeToObsEnd)에서 우리는 두 분포 사이의 사소한 차이만을 보고도, 우리의 가정이 유지된다는 것을 시사합니다.

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/timeToObsEnd.png" alt="Time to observation end for those that are censored, and those that are uncensored." width="100%" />
<p class="caption">(\#fig:timeToObsEnd)Time to observation end for those that are censored, and those that are uncensored.</p>
</div>

## Diagnostics for All Estimation

설계별 진단 후, 모든 인과 효과 추정 방법에 걸쳐 적용 가능한 몇 가지 진단이 있습니다. 이들 중 대부분이 대조 가설의 사용과 해답이 이미 알려진 연구 질문에 의존합니다. 대조군 가설을 활용하면 우리의 설계가 사실과 일치하는 결과를 산출하는지 평가할 수 있다. 대조군은 음성 대조군과 양성 대조군으로 나눌 수 있습니다.

### Negative Controls {#NegativeControls}

\index{negative controls}

음성 대조군은 인과적 효과가 없을 것으로 생각되는 노출-결과 쌍이며, 교란, [@lipsitch_2010] 선택 편향, 그리고 측정 오류를 감지하기 위한 수단으로 권장되어온 음성 대조군 혹은 “falsification endpoints”[@prased_2013] 을 포함합니다. [@arnold_2016] 예를 들어, 소아기 질병과 후기 다발성 경화증 (multiple sclerosis)의 관계를 조사하는 한 연구에서 [@zaadstra_2008], 저자는 다발성 경화증을 유발하지 않는 것으로 생각되는 세 가지 음성 대조군을 포함한다: 팔 골절, 뇌진탕, 편도선 절제술. 이 세 가지 대조군 중 두 가지는 다발성 경화증과 통계적으로 유의한 연관성을 나타내므로 연구가 편향 될 수 있음을 시사합니다. 

우리는 관심 가설과 비교 가능한 음성 대조군을 선택해야 합니다. 즉, 일반적으로 관심 가설과 동일한 결과 (“outcome controls” 라고 함) 또는 동일한 노출(“exposure controls”)를 갖는 노출-결과 쌍을 선택합니다. 부정적인 통제는 다음 기준을 충족해야 합니다:

- 노출이 결과의 **원인이 되어선 안 됩니다**. 인과 관계를 생각하는 한 가지 방법은 반사실적인 사고입니다: 환자가 노출 된 경우와 비교하여 환자가 노출되지 않은 경우에서 결과가 초래 (혹은 예방) 될 수 있습니까? 때로는 명확하게, 예를 들어 ACEi는 혈관 부종을 유발하는 것으로 알려져있습니다. 다른 때는 훨씬 덜 명확하게, 예를 들어 고혈압을 유발할 수 있는 약물은 고혈압의 결과인 심혈관 질환을 간접적으로 유발 시킬 수 있습니다.
- 노출은 또한 결과를 **예방하거나 치료해서도 안** 된다. 이것은 실제 효과 크기가 1이라고 믿어야 할 경우 또 다른 인과 관계입니다.
- 음성 대조군은 데이터에서 존재해야 하며, 이상적으로는 충분한 숫자를 가지고 있어야 합니다. 우리는 음성 대조군들의 유병률에 따라 우선시함으로써 달성합니다.
- 음성 대조군은 이상적으로 **독립**이어야 합니다. 예를 들어, 서로의 조상 (e.g. “ingrown nail” and “ingrown nail of foot”)이거나, 형제 자매 (e.g. “fracture of left femur” and “fracture of right femur”)인 음성 대조군은 피해야 합니다.
- 음성 대조군은 이상적으로 어느 정도의 **편향의 가능성**이 있어야 합니다. 예를 들어, 누군가의 사회 보장 번호의 마지막 숫자는 기본적으로 임의의 숫자이며, 교란을 의미하진 않습니다. 따라서, 음성 대조군으로 사용해서는 안됩니다.

음성 대조군이 노출-결과 쌍과 동일한 교란 요인 구조를 가져야 한다는 주장도 있습니다. [@lipsitch_2010]그러나, 우리는 이 교란 구조는 알 수 없다고 믿습니다. 현실에서 발견되는 변수들 사이의 관계는 때때로 사람들이 상상하는 것보다 훨씬 더 복잡합니다. 또한, 교란요인의 구조가 알려진 경우에도, 정확히 동일한 구조를 가지고 있지만 직접적인 인과 효과가 없는 음성 대조군이 존재할 가능성은 낮습니다. 이런 이유로 OHDSI에서는 이러한 가설이 관심 가설에 포함 된 것을 포함하여 여러 가지 유형의 편향을 나타낸다고 가정 할 때 많은 수의 음성 대조군에 의존합니다. 이러한 이유로 OHDSI에서 우리는 그러한 집합이 관심 가설에 존재하는 것을 포함하여, 많은 유형의 편향을 나타낸다고 가정하여 많은 수의 음성 대조군에 의존합니다.

노출과 결과 사이에 인과 관계가 없다는 것은 거의 문서화 되어있지 않습니다. 그 대신에, 우리는 종종 연관성에 대한 근거가 부족하다는 것이 연관성의 결여를 의미한다고 가정합니다. 노출과 결과 모두 광범위하게 연구되었으므로 연관성이 탐지 될 수 있었다면 이 가정은 유지 될 가능성이 더 높습니다. 예를 들어, 완전히 새로운 약물에 대한 근거의 부족은 연관성의 부족이 아니라 지식의 부족을 의미합니다. 이러한 원칙을 염두에 두고 음성 대조군을 선택하기 위한 반자동적인 절차를 개발했습니다. [@voss_2016] 간략하게, 문헌, 제품 라벨 및 자발적 보고의 정보가 자동적으로 추출 및 합성되어 음성 대조군의 후보 목록을 생성합니다. 이 목록은 자동화된 추출이 정확하다는 것을 검증할뿐만 아니라 생물학적 타당성과 같은 추가 기준을 부과하기 위해 직접 검토를 거쳐야만 합니다.

### Positive Controls {#PositiveControls}

\index{positive controls}

실제 상대비가 1보다 작거나 클 때 방법의 행동을 이해하려면 귀무가설이 참이 아닌 것으로 간주되는 양성 대조군을 사용해야 합니다. 불행하게도, 관측 연구에 대한 실제 양성 대조군은 세 가지 이유로 문제가 되는 경향이 있습니다. 첫째, 대부분의 연구 상황에서, 예를 들어 두 치료의 효과를 비교할 때, 특정 상황과 관련된 양성 대조군의 부족이 있습니다. 둘째, 양성 대조군이 가능하더라도, 효과 크기의 규모는 정확하게 알려지지 않았을 수 있으며, 때때로 효과 크기를 측정하는 모집단에 의존합니다. 셋째, 치료가 특정 결과를 유발하는 것으로 널리 알려진 경우, 예를 들어 원치 않는 결과의 위험을 완화시키기 위한 조치를 취하는 것 즉, 치료를 처방하게끔 의사의 행동을 형성하는 것은, 양성 대조군을 평가 수단으로 사용 할 수 없게 만듭니다. [@noren_2014]

\index{positive controls!synthesis}

노출 위험이 있는 기간 동안 음성 대조군을 수정하여 생성된 합성 양성 대조군을 추가로 주입함으로써, [@schuemie_2018] 결과가 발생한 것처럼 보이게 모의합니다.예를 들어, ACEi에 노출되는 동안, 음성 대조군의 결과 “내성 손톱”이 n 번 발견되었다고 가정합니다. 노출 중에 n 개의 모의 발생을 추가하면 위험이 두 배가 됩니다. 이것이 음성 대조군이였기 때문에, 반사실적으로 비교했을 때의 상대 위험은 1이지만, 주입 후에는 2가 됩니다.

중요한 문제 중 하나는 교란을 유지하는 것입니다. 음성 대조군은 강력한 교란을 보일 수 있지만, 추가 결과를 무작위로 주입하게 된다면 이러한 새로운 결과는 교란되지 않으므로 양성 대조군을 위한 교란을 대하는 능력을 평가하게 될 수 있습니다. 교란을 보존하기 위해, 우리는 새로운 결과가 환자 특정의 기저 공변량과 유사한 연관성을 기존의 결과로서 보여주기를 원합니다. 이를 달성하기 위해 각 결과에 대해 노출 전에 포착 된 공변량을 사용하여 노출 중 결과에 대한 생존율을 예측하는 모델을 시킵니다. 공변량들은 인구 통계학적 뿐만 아니라, 기록된 모든 진단, 약물 노출, 측정 및 의료 처치가 포함됩니다. 제약 초모수를 선택하기 위해 10회 교차 검증된 L1-regularized Poisson regression [@suchard_2013] 예측 모형을 적합 시킵니다. 그런 다음, 예측된 비율을 사용하여 실제 효과 크기를 원하는 규모로 늘리기 위해 노출 동안 모의한 결과를 표집합니다. 결과적으로, 양성 대조군은 실제 결과와 모의한 결과 모두를 포함합니다. 

그림 \@ref(fig:posControlSynth) 이 과정을 보여줍니다. 이 절차는 몇 가지 중요한 편향을 편향 요인을 모의하지만, 모든 것을 포착하진 않습니다. 예를 들어, 측정 오류의 일부 효과가 없습니다. 합성 양성 대조군은 일정한 양성 예측도와 민감도를 암시하지만, 실제로는 그렇지 않을 수 있습니다.

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/posControlSynth.png" alt="Synthesizing positive controls from negative controls." width="90%" />
<p class="caption">(\#fig:posControlSynth)Synthesizing positive controls from negative controls.</p>
</div>

각각의 대조군에 대해 하나의 실제 “효과 크기”를 언급하지만, 상이한 방법은 상이한 치료 효과 통계량을 추정합니다. 인과 관계가 존재하지 않는다고 판단되는 음성 대조군의 경우, 상대 위험, 위험 비, 오즈 비, 발생률 비, 조건부와 한계, as well as the average treatment effect in the treated (ATT) and the overall average treatment effect (ATE)를 포함한 모든 통계량은 1로 동일합니다. 양성 대조군을 생성하는 우리의 과정은 한계 효과가 달성되는 지점까지 이 비율이 일정하게 유지되는 환자에 대해 조건부 모형을 사용하여 시간에 따라, 그리고 환자간에 일정한 발생률 비율로 결과를 합성합니다. 따라서 실제 효과 크기는 치료에서 한계 발생률 비율로 유지됩니다. 합성 중에 사용된 결과 모델이 정확하다는 가정 하에 조건부 효과 크기와 ATE도 동일합니다. 모든 결과가 드물기 때문에 오즈 비는 상대 비와 거의 동일합니다.

### Empirical Evaluation {#metrics}

\index{empirical evaluation}

음성 및 양성 대조군에 대한 특정 방법의 추정치를 기반으로 다음과 같은 다양한 측정 기준을 계산하여 작동 특성을 이해할 수 있습니다:

*	**Area Under the receiver operator Curve (AUC)**: 양성 대조군과 음성 대조군 구별력. 
*	**Coverage**: 실제 효과 크기가 95% 신뢰 구간 내에 포함된 정도.
*	**Mean precision**: 정밀도는 $ 1 / (standard \ error) ^ 2 $로 계산되며 정밀도가 높을수록 신뢰구간이 좁아집니다. 기하 평균을 사용하여 정밀도의 치우친 분포를 설명합니다.
*	**Mean squared error (MSE)**: 효과 크기의 점추정치 로그와 실제 효과 크기 로그 사이의 평균 제곱 오차.
*	**Type 1 error**: 음성 대조군의 경우, 귀무가설이 기각된 정도 (at $\alpha = 0.05$). 이는 위 양성도 및 $1 - specificity$에 해당합니다.
*	**Type 2 error**: 양성 대조군의 경우, 귀무가설이 기각되지 않는 정도 (at $\alpha = 0.05$). 이는 위 음성도 및 $1 - sensitivity$에 해당합니다.
*	**Non-estimable**: 대조군 방법에서 추정치를 산출할 수 없었던 대조군은 몇 개인가? 추정 할 수 없는 이유는 여러 가지가 있을 수 있는데, 예를 들어 성향 점수 매칭 후 남은 환자나 결과를 가진 환자들이 없는 경우입니다.

활용 사례에 따라 이러한 작동 특성이 우리의 목표에 적합한지 평가할 수 있습니다. 예를 들어, signal detection을 수행하고자 하는 경우, type 1과 type 2 오류를 고려하거나 $\alpha$의 임계 값을 수정하려는 경우 AUC를 대신 검사 할 수 있습니다.

### P-Value Calibration

\index{p-value calibration} \index{empirical calibration}

때로는 type 1 error (at $\alpha = 0.05$)가 5%보다 큽니다. 다시 말해서, 실제로 귀무 가설이 참임에도 불구하고 귀무 가설을 기각 할 가능성이 5% 이상인 경우가 많습니다. 그 이유는 p-value는 임의 오차 만 반영하기 때문입니다. 즉, 제한된 표본 크기로 인한 오차가 발생합니다. 예를 들어, 교란으로 인해 발생한 체계적인 오차의 경우 반영되지 않습니다. OHDSI는 p-value를 보정하여 type 1 error를 공칭 으로 복원하는 과정을 개발했습니다. [@schuemie_2014] 우리는 음성 대조군에 대한 실제 효과 추정치로부터 경험적 귀무 분포를 유도합니다. 음성 대조군의 추정치는 귀무 가설이 참일 때 기대 할 수 있는 것에 대한 지표를 제공하며 경험적 귀무 분포를 추정하는 데 사용합니다.

공식적으로, 우리는 각 추정치의 표집 오차를 고려하여 추정치에 가우스 확률 분포를 적합시킵니다. $\hat{\theta}_i$ 는 $i$번째 음성 대조군 약물-결과 쌍으로 추정된 로그 효과 추정치 (상대 위험, 오즈 혹은 발생률 비)를 나타내고, $\hat{\tau}_i$ 는 이에 해당하는 추정된 표준 오차  $i=1,\ldots,n$ 를 나타낸다. $\theta_i$ 는 실제 로그 효과 크기를 나타내고 (음성 대조군의 경우 0으로 가정), $\beta_i$가 실제 (하지만 알 수 없는) 짝지어진 $i$ 와 연관된 편향을 나타냅니다. 즉, 실제 효과 크기의 로그와 연구에서 $i$ 로 반환될 것으로 추정하는 값의 차이는 무한으로 커집니다. 일반적인 p-value 계산처럼, 우리는 $\hat{\theta}_i$ 가 평균이 $\theta_i + \beta_i$ 이고, 표준편차가 $\hat{\tau}_i^2$ 인 정규 분포를 따른다고 가정합니다. 전통적인 p-value 계산에서, $\beta_i$ 는 항상 0과 같다고 가정하지만, $\beta_i$ 는 평균이 $\mu$ 이고 분산이 $\sigma^2$ 인 정규분포에서 구해집니다. This represents the null (bias) distribution. 우리는 최대 우도를 통해 $\mu$ 와 $\sigma^2$ 를 추정합니다. 요약해서 우리는 다음과 같이 가정합니다:

$$\beta_i \sim N(\mu,\sigma^2) \text{  and  } \hat{\theta}_i \sim N(\theta_i + \beta_i, \tau_i^2)$$

$N(a,b)$는 평균 $a$와 분산 $b$를 갖는 가우시안 분포를 나타내고, 다음과 같이 우도를 최대화하여 $\mu$와 $\sigma^2$을 추정하여:

$$L(\mu, \sigma | \theta, \tau) \propto \prod_{i=1}^{n}\int p(\hat{\theta}_i|\beta_i, \theta_i, \hat{\tau}_i)p(\beta_i|\mu, \sigma) \text{d}\beta_i$$

최대우도추정량 $\hat{\mu}$ 과 $\hat{\sigma}$를 얻습니다. 우리는 경험적 귀무 분포를 이용하여 보정된 p-value를 계산합니다. 여기서, $\hat{\theta}_{n+1}$은 새로운 약물-결과 쌍으로부터 추정된 효과의 로그를 나타내고, $\hat{\tau}_{n+1}$는 이에 상응하는 표준 오차입니다. 앞서 언급한 가정으로부터, 동일한 귀무 분포로부터 $\beta_{n+1}$를 얻는다고 가정할 경우 다음과 같다:

$$\hat{\theta}_{n+1} \sim N(\hat{\mu}, \hat{\sigma} + \hat{\tau}_{n+1})$$

$\hat{\theta}_{n+1}$이 $\hat{\mu}$ 보다 작은 경우, 새로운 쌍에 대해 보정된 단측 p-value는:

$$\phi\left(\frac{\theta_{n+1} - \hat{\mu}}{\sqrt{\hat{\sigma}^2 + \hat{\tau}_{n+1}^2}}\right)$$

여기서 $\phi(\cdot)$ 은 표준 정규 분포의 누적 분포 함수를 나타냅니다. $\hat{\theta}_{n+1}$이 $\hat{\mu}$보다 클 때, 보정된 단측 p-value는:

$$1-\phi\left(\frac{\theta_{n+1} - \hat{\mu}}{\sqrt{\hat{\sigma}^2 + \hat{\tau}_{n+1}^2}}\right)$$

### Confidence Interval Calibration

\index{confidence interval calibration}

마찬가지로, 우리는 일반적으로 95% 신뢰 구간의 적용 범위가 95% 미만임을 관찰합니다: the true effect size is inside the 95% confidence interval less than 95% of the time. 신뢰 구간의 보정 [@schuemie_2018] 을 위해 우리는 양성 대조군을 사용하여 p-value 보정을 위한 프레임 워크를 확장합니다. 일반적으로 보정된 신뢰 구간은 공칭 신뢰 구간보다 넓지만 표준적인 절차에서는 설명되지 않던 문제들 (측정되지 않은 교란, 선택적 편향, 그리고 측정 오차와 같은)이 반영됩니다.

공식적으로, 우리는 쌍 $i$ 와 관련된 편향인 $beta_i$ 가 다시 가우시안 분포에서 나온다고 가정하지만, 이번에는 실제 효과 크기인 $theta_i$ 와 선형으로 관련된 평균 및 표준편차를 사용합니다:

$$\beta_i \sim N(\mu(\theta_i) , \sigma^2(\theta_i))$$

일때

$$\mu(\theta_i) = a + b \times \theta_i \text{ and } \\
  \sigma(\theta_i) ^2= c + d \times \mid \theta_i \mid$$

우리는 관찰되지 않은 $\beta_i$ 를 통합하는 주변 우도를 최대화하여 $a$, $b$, $c$ and $d$ 를 추정합니다:

$$l(a,b,c,d | \theta, \hat{\theta}, \hat{\tau} ) \propto \prod_{i=1}^{n}\int p(\hat{\theta}_i|\beta_i, \theta_i, \hat{\tau}_i)p(\beta_i|a,b,c,d,\theta_i) \text{d}\beta_i ,$$

 최대우도추정량인 $(\hat{a}, \hat{b}, \hat{c}, \hat{d})$ 를 얻습니다.

우리는 체계적 오차 모형을 사용하는 보정된 CI를 계산합니다. 여기서 $\hat{\theta}_{n+1}$는 다시 추정 효과의 로그를 다시 표시하고, $\hat{\tau}_{n+1}$는 해당하는 추정의 표준 오차를 나타냅니다. 위 가정에서 $\beta_{n+1}$이 동일한 체계 오차 모형에서 발생한다고 가정하면 다음과 같습니다:

$$\hat{\theta}_{n+1} \sim N(
\theta_{n+1} + \hat{a} + \hat{b} \times \theta_{n+1},
\hat{c} + \hat{d} \times \mid \theta_{n+1} \mid) + \hat{\tau}_{n+1}^2) .$$

$\theta_{n+1}$ 에 대한 방정식의 해로 보정된 95 % CI의 하한을 찾습니다:

$$\Phi\left(
\frac{\theta_{n+1} + \hat{a} + \hat{b} \times \theta_{n+1}-\hat{\theta}_{n+1}}
{\sqrt{(\hat{c} + \hat{d} \times \mid \theta_{n+1} \mid) + \hat{\tau}_{n+1}^2}}
\right) = 0.025 ,$$

여기서 $\Phi(\cdot)$는 표준 정규 분포의 누적 분포 함수를 나타냅니다. 상한도 유사하게 확률 0.975에서 찾습니다. 확률 0.5를 이용하여 보정된 점 추정치를 정의합니다.

[EmpiricalCalibration]https://ohdsi.github.io/EmpiricalCalibration/) 패키지에서 p-값 보정과 신뢰 구간 보정이 모두 구현됩니다.

### Replication Across Sites

\index{between-database heterogeneity}

방법 검증의 또 다른 형태는 다른 인구와 다른 의료 시스템 혹은  데이터 수집 과정이 다른 데이터베이스에서 연구를 실행하는 것입니다. 기존 연구에서 서로 다른 데이터베이스에서 동일한 연구 설계를 실행하면 효과 크기 추정치가 크게 다른 것으로 나타났으며, [@madigan_2013] 이는 모집단마다 효과 크기가 다르거나 설계가 다른 데이터베이스에서 발견된 다양한 편향을 적절하게 해결하지 못하고 있음을 시사합니다. 실제로, 우리는 신뢰구간의 경험적 보정을 통한 데이터베이스의 잔차 편향을 고려하면 연구간의 이질성을 크게 줄일 수 있음을 관찰합니다. [@schuemie_2018]

데이터베이스간의 이질성을 표현하는 하나의 방법은 $I^2$  점수이며, 우연히 발생한 것이 아닌 이질성으로 인해 전체 연구에서 전체 분산의 백분율을 나타냅니다. [@higgins_2003] $I^2$ 값에 대해 단순한 분류는 모든 상황에서 적합하지 않긴 하지만, 25%, 50%, 그리고 75% 에서 $I^2$ 점수에 대해 낮음, 중간, 높음의 형용사를 임시로 할당 할 수 있습니다. 대규모 성향 점수 보정을 사용한 new-user cohort 설계를 사용하여 많은 우울증 치료 효과를 추정하는 연구에서, [@schuemie_2018b] 추정치의 58%만이 $I^2$ 의 25% 미만인 것으로 관찰되었습니다. 이는 경험적 보정 후에 83%로 증가했습니다.

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant">데이터베이스간의 이질성을 관찰하면 추정치의 유효성에 의문이 생깁니다. 불행하게도, 그 반대는 사실이 아닙니다. 이질성을 관찰하지 않는 것이 불편추정을 보장하지 않습니다. 모든 데이터베이스가 유사한 편향을 공유하며, 추정치가 일관되게 잘못되었을 가능성은 거의 없습니다.</div>\EndKnitrBlock{rmdimportant}

### Sensitivity Analyses

\index{sensitivity analysis}

연구를 설계 할 때, 때때로 설계 선택이 불확실합니다. 예를 들어, 층화 성향 점수 짝짓기를 사용해야 하는지? 층화를 한다면, 어느정도 층화를 해야하는가? 적절한 time-at-risk 는 무엇인가? 이러한 불확실성에 직면 할 때, 하나의 해결법은 다양한 옵션을 평가하고, 디자인 선택에 대한 결과의 민감도를 관찰하는 것입니다. 다양한 옵션에서 추정치가 일관되고 유지되면 불확실성에 대해 연구가 굳건하다고 말할 수 있습니다.

민감도 분석에 대한 이러한 정의는 “연구의 결론이 다양한 규모의 숨겨진 편향에 의해 어떻게 변경 될 수 있는지 평가” 라고 민감도 분석을 정의한 @rosenbaum_2005 과 같이 다른 연구자들이 사용하는 정의와 혼동되어서는 안됩니다.

## Method Validation in Practice

여기서, 우리는 thiazides and thiazide-like diuretics (THZ)에 비해 ACE 억제제 (ACEi)와 비교하여 혈관 부종 및 급성 심근 경색의 위험에 대한 ACEi의 효과를 조사하는 \@ref(PopulationLevelEstimation)장의 예를 바탕으로 합니다. 이 장에서는 우리가 이미 사용했던 설계, 즉 CohortMethod에 관한 많은 진단을 살펴 보았습니다. 여기에는 다른 디자인을 사용한 경우에도 적용 할 수 있는 추가 진단이 적용됩니다. \@ref(PleAtlas)절에 설명한대로, ATLAS를 사용하여 연구를 구현하는 경우 ATLAS에서 생성한 연구의 R패키지에 포함된 Shiny 앱에서 이러한 진단을 사용할 수 있습니다. 연구가 \@ref(pleR)절에 설명한 것처럼, R을 사용하여 구현된다면, 다음 절에서 설명한대로 다양한 패키지에서 사용할 수 있는 R함수를 사용해야 합니다.

### Selecting Negative Controls

우리는 인과 관계 영향이 없는 것으로 생각되는 음성 대조군, 노출-결과 쌍을 선택해야 한다. 우리의 예제 연구와 같은 비교 효과 추정을 위해, 우리는 목적이나 비교 노출로 인해 야기되지 않는다고 여겨지는 음성 대조군을 선택합니다. 우리는 대조군에서 다양하게 혼합된 편향을 확보하고 경험적 보정을 가능하도록 표현된 충분한 음성 대조군을 원합니다. 일반적으로 우리는 50-100개의 음성 대조군을 목표로 합니다. 우리는 이러한 대조군을 완전히 직접 만들 뻔 했지만, 다행히 ATLAS는 문헌, 제품 라벨 및 자발적 보고서의 데이터를 사용하여 음성 대조군을 선택 할 수 있는 기능을 제공합니다.

음성 대조군의 후보 목록을 생성하려면, 먼저 관심 있는 모든 노출을 포함하는 concept set을 생성해야 합니다. 이 경우 그림 \@ref(fig:exposuresConceptSet)과 같이 ACEi 및 THZ클래스의 모든 성분을 선택합니다.

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/exposuresConceptSet.png" alt="A concept set containing the concepts defining the target and comparator exposures." width="100%" />
<p class="caption">(\#fig:exposuresConceptSet)A concept set containing the concepts defining the target and comparator exposures.</p>
</div>

다음으로, ‘Explore Evidence” 탭으로 가서 ![](images/MethodValidity/generate.png) 버튼을 클릭합니다.근거 개요를 작성하는 데는 몇 분이 걸리고, 그 후 ![](images/MethodValidity/viewEvidence.png) 버튼을 클릭 할 수 있습니다. 그러면 그림 \@ref(fig:candidateNcs)에 표시된 결과와 같은 목록이 열립니다.

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/candidateNcs.png" alt="Candidate control outcomes with an overview of the evidence found in literature, product labels, and spontaneous reports." width="100%" />
<p class="caption">(\#fig:candidateNcs)Candidate control outcomes with an overview of the evidence found in literature, product labels, and spontaneous reports.</p>
</div>

이 목록은 진단 concept과 우리가 정의한 노출 중 하나를 연결하는 근거의 개요를 보여줍니다. 예를 들어, 다양한 전략을 사용하여 PubMed에서 찾은 결과와 노출을 연결하는 출판물 수, 우리의 관심이 되는 노출의 제품 라벨에서 발생 가능한 부작용 진단 리스트의 수, 그리고 자발적인 보고서 수를 볼 수 있습니다. 기본적으로 이 목록은 음성 대조군의 후보를 먼저 표시하도록 정렬됩니다. 그런 다음 관측형 데이터베이스에서 수집된 진단의 유병률을 나타내는 “Sort Order”로 정렬됩니다. Sort Order가 높을수록 유병률이 높습니다. 이 데이터베이스의 유병률은 우리가 연구를 수행하려는 데이터베이스의 유병률과 일치하지 않을 수 있지만, 좋은 근사치일 수 있습니다.

다음 단계는 일반적으로 후보의 유병률이 가장 높은 진단부터 시작하여, 충분히 납득 할 수 있을 때까지 대조군의 목록을 직접 검토하는 것입니다.  이 작업을 수행하는 일반적인 방법 중 하나는 목록을 CSV (쉼표로 구분 된 값) 파일로 내보내어 임상의가 \@ref(NegativeControls)절에서 언급 된 기준을 고려하여 이를 검토하는 것입니다.

예제 연구에서는 부록 \@ref(AceiThzNsc)에 나열된 76개의 음성 대조군을 선택합니다.

### Including Controls 

음성 대조군의 집합을 정의했다면, 그것을 연구에 포함시켜야 합니다. 우리는 먼저 음성 대조군의 concept을 결과 코호트로 바꾸기 위한 몇 가지 논리를 정의해야 합니다. \@ref(evaluationSettings)절은 ATLAS가 사용자가 선택해야 하는 몇 가지 선택을 기반으로 이러한 코호트를 생성하는 방법을 설명합니다. 우리는 종종 음성 대조군의 concept 혹은 그 자손의 발생에 기초하여 코호트를 만들기로 선택합니다. 본 연구가 R에서 구현된다면, SQL (Structured Query Language)을 사용하여 음성 대조군 코호트를 구성 할 수 있습니다. \@ref(SqlAndR)장에서는 SQL과 R을 사용하여 코호트를 만드는 방법을 설명합니다. 독자가 적절한 SQL과 R을 작성하는 연습을 남겨둡니다.

OHDSI 도구는 음성 대조군에서 파생된 양성 대조군을 자동으로 생성하고 포함하는 기능 역시 제공합니다. 이 기능은 \@ref(evaluationSettings)절에서 설명된 ATLAS Evaluation Settings에서 찾을 수 있으며, [MethodEvaluation](https://ohdsi.github.io/MethodEvaluation/) 패키지의 `syntheticPositiveControls`함수에서 구현됩니다. 여기서 생존 모델을 사용하여 실제 효과 크기가 1.5, 2, 그리고 4인 각 음성 대조군에 대해 세 가지 양성 대조군을 생성합니다:


```r
library(MethodEvaluation)
# Create a data frame with all negative control exposure-
# outcome pairs, using only the target exposure (ACEi = 1).
eoPairs <- data.frame(exposureId = 1,
                      outcomeId = ncs)

pcs <- synthesizePositiveControls(
  connectionDetails = connectionDetails,
  cdmDatabaseSchema = cdmDbSchema,
  exposureDatabaseSchema = cohortDbSchema,
  exposureTable = cohortTable,
  outcomeDatabaseSchema = cohortDbSchema,
  outcomeTable = cohortTable,
  outputDatabaseSchema = cohortDbSchema,
  outputTable = cohortTable,
  createOutputTable = FALSE,
  modelType = "survival",
  firstExposureOnly = TRUE,
  firstOutcomeOnly = TRUE,
  removePeopleWithPriorOutcomes = TRUE,
  washoutPeriod = 365,
  riskWindowStart = 1,
  riskWindowEnd = 0,
  endAnchor = "cohort end",
  exposureOutcomePairs = eoPairs,
  effectSizes = c(1.5, 2, 4),
  cdmVersion = cdmVersion,
  workFolder = file.path(outputFolder, "pcSynthesis"))
```

우리의 추정 연구 설계에 사용된 time-at-risk를 모방해야 합니다. `synthesizePositiveControls`함수는 노출과 음성 대조군 결과에 대한 정보를 추출하고, 쌍마다의 모델링 결과를 적합하고, 결과를 합성합니다. 양성 대조군의 결과 코호트는 `cohortDbSchema`와 `cohortTable`로 지정된 코호트 테이블에 추가됩니다. 결과인 pcs data frame은 합성된 양성 대조군에 대한 정보가 포함됩니다.

다음으로, 우리는 음성 및 양성 대조군에 대한 영향을 추정하기 위해 관심 효과를 추정하는 데 사용된 동일한 연구를 실행해야 합니다. ATLAS의 비교 대화상자에서 양성 대조군 세트를 설정하면, ATLAS가 이러한 대조군에 대한 추정치를 계산하도록 합니다. 마찬가지로 Evaluation Settings에서 양성 대조군을 생성하도록 지정하면, 분석에 이러한 대조군이 포함됩니다. R에서, 음성 및 양성 대조군은 서로 다른 결과로 취급되어야 합니다. [OHDSI Methods Library](https://ohdsi.github.io/MethodsLibrary/) 의 모든 추정 패키지는 효율적인 방식으로 많은 효과를 쉽게 추정 할 수 있습니다.

### Empirical Performance

그림 \@ref(fig:controls)는 예제 효과 연구에 포함된 음성 및 양성 대조군에 대한 추정 효과 크기를 실제 효과 크기로 계층화 한 것입니다. 이 plot은 ATLAS에서 생성한 R 패키지와 함께 제공되는 Shiny 앱에 포함되어 있으며, [MethodEvaluation](https://ohdsi.github.io/MethodEvaluation/) 패키지의 `plotControls` 함수를 사용하여 생성 할 수 있습니다. 추정치를 생성하거나 양성 대조군을 합성하기에 충분한 데이터가 없기 때문에 대조군의 수는 때때로 정의 된 것보다 적을 수 있습니다.

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/controls.png" alt="Estimates for the negative (true hazard ratio = 1) and positive controls (true hazard ratio &gt; 1). Each dot represents a control. Estimates below the dashed line have a confidence interval that doesn't include the true effect size." width="100%" />
<p class="caption">(\#fig:controls)Estimates for the negative (true hazard ratio = 1) and positive controls (true hazard ratio > 1). Each dot represents a control. Estimates below the dashed line have a confidence interval that doesn't include the true effect size.</p>
</div>

이러한 추정치를 기반으로 [MethodEvaluation](https://ohdsi.github.io/MethodEvaluation/) 패키지의 `computeMetrics` 함수를 사용하여 표 \@ref(tab:exampleMetrics)에 표시된 측정 기준을 계산할 수 있습니다.

Table: (\#tab:exampleMetrics) Method performance metrics derived from the negative and positive control estimates.

| Metric         | Value |
|:-------------- | -----:|
| AUC            |  0.96 |
| Coverage       |  0.97 |
| Mean Precision | 19.33 |
| MSE            |  2.08 |
| Type 1 error   |  0.00 |
| Type 2 error   |  0.18 |
| Non-estimable  |  0.08 |

적용 범위와 type 1 error는 각각 공칭 값인 95%와 5에 매우 가깝고, AUC는 매우 높습니다. 항상 그런 것은 아닙니다.

그림 \@ref(fig:controls)에서 실제 위험 비율이 1일 때 모든 신뢰 구간에 1이 포함되는 것은 아니지만 표 \@ref(tab:exampleMetrics)의 type 1 error는 0%입니다. [Cyclops](https://ohdsi.github.io/Cyclops/) 패키지의 신뢰 구간은 우도를 profiling 을 사용하여 추정되므로 기존 방법보다 정확하지만 비대칭적인 신뢰 구간이 발생할 수 있기 때문에 예외적인 상황입니다. 대신 p-value는 대칭신뢰 구간을 가정하여 계산되며 이는 type 1 error를 계산하는 데 사용 된 것입니다.

### P-Value Calibration

p-value을 보정하기 위해 음성 대조군에 대한 추정값을 사용 할 수 있습니다. 이는 Shniy 앱에서 자동으로 수행되며, R에서 수동으로도 수행 할 수 있습니다. \@ref(MultipleAnalyses)절에 설명 된대로 요약된 객체 summ을 생성했다고 가정하면 경험적 보정 효과 plot을 그릴 수 있습니다:


```r
# Estimates for negative controls (ncs) and outcomes of interest (ois):
ncEstimates <- summ[summ$outcomeId %in% ncs, ]
oiEstimates <- summ[summ$outcomeId %in% ois, ]

library(EmpiricalCalibration)
plotCalibrationEffect(logRrNegatives = ncEstimates$logRr,
                      seLogRrNegatives = ncEstimates$seLogRr,
                      logRrPositives = oiEstimates$logRr,
                      seLogRrPositives = oiEstimates$seLogRr,
                      showCis = TRUE)
```
<div class="figure" style="text-align: center">
<img src="images/MethodValidity/pValueCal.png" alt="P-value calibration: estimates below the dashed line have a conventional p &lt; 0.05. Estimates in the shaded area have calibrated p &lt; 0.05. The narrow band around the edge of the shaded area denotes the 95\% credible interval. Dots indicate negative controls. Diamonds indicate outcomes of interest." width="70%" />
<p class="caption">(\#fig:pValueCal)P-value calibration: estimates below the dashed line have a conventional p < 0.05. Estimates in the shaded area have calibrated p < 0.05. The narrow band around the edge of the shaded area denotes the 95\% credible interval. Dots indicate negative controls. Diamonds indicate outcomes of interest.</p>
</div>

그림 \@ref(fig:pValueCal)에서 우리는 음영 영역이 점선으로 표시된 영역과 거의 정확하게 겹치는 것을 볼 수 있는데, 이는 음성 대조군에 대해 어떠한 바이어스도 관찰되지 않았음을 나타낸다. 관심있는 결과 중 하나 (급성 심근 경색)는 점선과 음영 위에 있으며, 보정 되거나 되지 않은 두 p-value에 따라 귀무 가설을 기각 할 수 없음을 나타냅니다. 다른 결과 (혈관 부종)는 음성 대조군에서 분명히 두드러지며 보정 되거나 되지 않은 두 p-value 모두 0.05보다 작은 영역에 잘 포함됩니다.

보정된 p-value를 계산할 수 있습니다:


```r
null <- fitNull(logRr = ncEstimates$logRr,
                seLogRr = ncEstimates$seLogRr)
calibrateP(null,
           logRr= oiEstimates$logRr,
           seLogRr = oiEstimates$seLogRr)
```

```
## [1] 1.604351e-06 7.159506e-01
```

그리고 이것들을 보정되지 않은 p-value와 비교하면:


```r
oiEstimates$p
```

```
## [1] [1] 1.483652e-06 7.052822e-01
```

예상한대로, 편향이 거의 없거나 전혀 관찰되지 않았기 때문에 보정되지 않은 p-value는 보정된 값과 매우 유사합니다.

### Confidence Interval Calibration

마찬가지로, 음성 및 양성 대조군에 대한 추정값을 사용하여 신뢰 구간을 보정 할 수 있습니다. Shiny 앱은 보정 신뢰 구간을 자동으로 보고합니다. R에서는 ["Empirical calibration of confidence intervals" vignette](https://ohdsi.github.io/EmpiricalCalibration/articles/EmpiricalCiCalibrationVignette.html) 에 자세히 설명된 대로, [EmpiricalCalibration](https://ohdsi.github.io/EmpiricalCalibration/) 패키지의 `fitSystematicErrorModel`및 `calibrateConfidenceinterval`함수를 사용하여 구간을 보정 할 수 있습니다.

보정하기 전에, 혈관 부종 및 급성 심근 경색의 추정 위험 비 (95% 신뢰구간)은 4.32 (2.45-8.08) 및 1.13 (0.59-2.18)입니다. 보정된 위험비는 4.75 (2.52-9.04) 및 1.15 (0.58-2.30)입니다.

### Between-Database Heterogeneity

한 데이터베이스에서 분석을 수행한 것처럼, IBM MarketScan Medcaid (MDCD) 데이터베이스에서, 우리는 다른 공통 데이터 모델 (CDM) 데이터베이스에서도 동일한 분석 코드를 실행할 수 있습니다. 그림 \@ref(fig:forest)은 혈관 부종의 결과에 대한 총 5개의 데이터베이스에 대한 forest plot과 메타 분석 추정치 (임의 요인을 가정) [@dersimonian_1986] 를 보여줍니다. 이 그림은 [EvidenceSynthesis](https://ohdsi.github.io/EvidenceSynthesis/) 패키지의 `plotMetaAnalysisForest`함수를 사용하여 생성되었습니다.

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/forest.png" alt="Effect size estimates and 95\% confidence intervals (CI) from five different databases and a meta-analytic estimate when comparing ACE inhibitors to thiazides and thiazide-like diuretics for the risk of angioedema." width="90%" />
<p class="caption">(\#fig:forest)Effect size estimates and 95\% confidence intervals (CI) from five different databases and a meta-analytic estimate when comparing ACE inhibitors to thiazides and thiazide-like diuretics for the risk of angioedema.</p>
</div>

모든 신뢰 구간이 1 이상이지만 효과가 있다는 사실에 대한 합의를 제안하지만 $I^2$는 데이터베이스 간 이질성을 제안합니다.

그러나 그림 \@ref(fig:forestCal)과 같이 보정된 신뢰구간을 사용하여 $I^2$를 계산하면 이질성이 음성 및 양성 대조군을 통해 각 데이터베이스에서 측정된 편향에 의해 설명 될 수 있음을 알 수 있습니다. 경험적 보정은 이 편향을 올바르게 고려한 것으로 보입니다. 

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/forestCal.png" alt="Calibrated Effect size estimates and 95\% confidence intervals (CI) from five different databases and a meta-analytic estimate for the hazard ratio of angioedema when comparing ACE inhibitors to thiazides and thiazide-like diuretics." width="90%" />
<p class="caption">(\#fig:forestCal)Calibrated Effect size estimates and 95\% confidence intervals (CI) from five different databases and a meta-analytic estimate for the hazard ratio of angioedema when comparing ACE inhibitors to thiazides and thiazide-like diuretics.</p>
</div>

### Sensitivity Analyses

분석에서 선택 된 설계 중 하나는 성향 점수에 variable-ratio을 사용하는 것이었습니다. 그러나 우리는 성향 점수에 계층화를 사용할 수도 있습니다. 우리는 이 선택에 대해 불확실하기 때문에, 둘 다 사용하기로 결정 할 수 있습니다. 표 \@ref(tab:sensAnalysis)는 variable-ratio 짝짓기 및 계층화 (10개의 동일한 크기의 strata)를 사용할 때 보정 및 보정되지 않은 급성 심근 경색 및 혈관 부종의 효과 크기 추정치를 보여줍니다.

Table: (\#tab:sensAnalysis) Uncalibrated and calibrated hazard ratios (95\% confidence interval) for the two analysis variants.

| Outcome | Adjustment | Uncalibrated | Calibrated |
|:-------------------- |:---------- | ------------- | ------------- |
| Angioedema | Matching | 4.32 (2.45 - 8.08) | 4.75 (2.52 - 9.04) |
| Angioedema | Stratification | 4.57 (3.00 - 7.19) | 4.52 (2.85 - 7.19) |
| Acute myocardial infarction | Matching | 1.13 (0.59 - 2.18) | 1.15 (0.58 - 2.30) |
| Acute myocardial infarction | Stratification | 1.43 (1.02 - 2.06) | 1.45 (1.03 - 2.06) |

짝짓기와 계층화 된 분석으로부터 얻은 추정치가 강하게 일치하는 것과 함께, 계층화에 대한 신뢰구간이 짝짓기된 신뢰 구간 내에 완전히 포함되는 것을 볼 수 있습니다. 이는 연구 설계 선택에 대한 우리의 불확실성이 추정치의 타당성에 영향을 미치지 않음을 시사합니다. 계층화는 우리에게 더 큰 power (더 좁은 신뢰 구간)를 제공하는 것으로 보이지만, 짝짓기는 많은 데이터의 손실을 초래하지만, 계층화는 그렇지 않기 때문에 놀라운 일은 아닙니다. 보정된 신뢰구간에 편향이 증가했다는 증거는 보이지 않지만 계층 내 잔류 교란으로 인한 대가로 편향이 증가 할 수 있습니다.

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant">연구 진단을 통해 연구를 완전히 실행하기 전에 설계 선택을 평가 할 수 있습니다. 모든 연구 진단을 생성하고 검토하기 전에 프로토콜을 완성시키지 않는 것이 좋습니다. P-hacking (원하는 결과를 얻도록 디자인을 조정), 원하는 효과 크기 추정값에 눈가림 된 채로 수행해야 합니다.</div>\EndKnitrBlock{rmdimportant}

## OHDSI Methods Benchmark

\index{OHDSI Methods Benchmark}

Although the recommended practice is to empirically evaluate a method’s performance within the context that it is applied, using negative and positive controls that are in ways similar to the exposures-outcomes pairs of interest (for example using the same exposure or the same outcome) and on the database used in the study, there is also value in evaluating a method’s performance in general. 이것이 OHDSI Methods Evaluation이 개발 된 이유입니다. 성능 평가 기준은 만성 혹은 급성 결과 또는 단기 노출을 포함한 광범위한 대조군 질의를 사용하여 성능을 평가합니다. 이 성능 평가 기준의 결과는 분석 방법의 전반적인 유용성을 입증하는데 도움이 될 수 있으며 상황별로 경험적 평가를 아직 사용 할 수 없는 경우 분석법의 성능에 대한 사전 확신을 형성하는데 사용될 수 있습니다. 사용 평가 기준은 8개의 범주로 분류 할 수 있는 200개의 신중하게 선택된 음성 대조군으로 구성되며 각 범주의 대조군은 동일한 노출 또는 동일한 결과를 공유합니다. 이러한 200개의 음성 대조군으로부터, \@ref(PositiveControls)절에 기술된 바와 같이 600개의 합성 양성 대조군이 유도됩니다. 분석 방법을 평가하려면 모든 대조군에 대해 효과 크기 추정치를 생성하는데 사용해야하며, 그 후 \@ref(metrics)절에 설명된 측정기준을 계산할 수 있습니다. 성능 평가 기준은 공개적으로 사용이 가능하며 [MethodEvaluation](https://ohdsi.github.io/MethodEvaluation/) 패키지의 [Running the OHDSI Methods Benchmark vignette](https://ohdsi.github.io/MethodEvaluation/articles/OhdsiMethodsBenchmark.html) 비네팅 실행에 설명된대로 배포 할 수 있습니다.

이 성능 평가 기준을 통해 OHDSI Methods Library의 모든 분석법마다의 방법으로 실행했습니다. 예를 들어, CohortMethod는 성향 점수 짝짓기, 층화, 그리고 가중화를 사용하여 평가했습니다. 이 실험은 4개의 대규모 관찰형 의료 데이터베이스에서 실행되었습니다. 온라인상의 Shiny app[^methodEvalViewerUrl] 에서 볼 수 있는 결과는 여러 방법이 높은 AUC (음성과 양성 대조군을 구분하는 능력)를 보여주지만, 그림 \@ref(fig:methodEval)와 같이, 대부분의 설정에서 대부분의 방법은 높은 type 1 error와 95% 신뢰 구간에 포함되는 비율이 낮은 것을 보여줍니다.

[^methodEvalViewerUrl]: http://data.ohdsi.org/MethodEvalViewer/

<div class="figure" style="text-align: center">
<img src="images/MethodValidity/methodEval.png" alt="Coverage of the 95\% confidence interval for the methods in the Methods Library. Each dot represents the performance of a specific set of analysis choices. The dashed line indicates nominal performance (95\% coverage). SCCS = Self-Controlled Case Series, GI = Gastrointestinal, IBD = inflammatory bowel disease." width="100%" />
<p class="caption">(\#fig:methodEval)Coverage of the 95\% confidence interval for the methods in the Methods Library. Each dot represents the performance of a specific set of analysis choices. The dashed line indicates nominal performance (95\% coverage). SCCS = Self-Controlled Case Series, GI = Gastrointestinal, IBD = inflammatory bowel disease.</p>
</div>


이것은 경험적 평가와 보정의 필요성을 강조합니다: 거의 모든 발표된 관찰연구에 대해 사실인 경험적 평가가 수행되지 않는다면, 우리는 그림 \@ref(fig:methodEval)의 결과와 같이 사전에 알려진 것을 가정해야 하며, 실제 효과 크기가 95% 신뢰 구간에 포함되지 않을 가능성이 높다고 결론지어야 합니다!

Method Library의 설계에 평가에서 경험적 교정은 종종 type 2 error를 증가시키고 정밀도를 낮추는 대가를 지불하지만, type 1 error와 포함 범위를 공칭 값으로 복원하는 것을 보여줍니다.

## Summary

\BeginKnitrBlock{rmdsummary}<div class="rmdsummary">- 방법의 타당성은 연구 방법의 기본 가정이 충족되는지 여부에 따라 다릅니다.

- 가능하면 이러한 가정은 실험 진단을 사용하여 경험적으로 검사를 해야 합니다.

- 답이 알려진 질문 대조군 가설을 사용하여 특정 연구 설계가 실제와 일치하는 답을 생성하는지 평가해야 합니다.

- 종종 p-value와 신뢰 구간은 대조군 가설을 사용하여 측정한 공칭 특성을 나타내지 않습니다.

- 이러한 특성은 경험적 보정을 사용하여 공칭으로 복원 될 수 있습니다.

- 연구자가 p-hacking을 피하기 위해 관심 있는 효과에 대해 눈가림된 분석 설계 선택을 안내하고 프로토콜을 개선하는 데 사용될 수 있습니다.
</div>\EndKnitrBlock{rmdsummary}
