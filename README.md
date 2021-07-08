# Project : News-Article-Issue-Tracking

<img width="30%" src="/images/01_Intro.jpg"/>

## 1. Introduction

여론을 파악하는 것은 현재의 비즈니스를 점검하거나, 미래의 인사이트를 도출할 수 있는 기회를 제공합니다. 

이러한 여론은 전통적으로 지면 뉴스(Paper News), 라디오, TV 등으로 유통되었지만 최근에는 인터넷 뉴스(Internet News), 소셜미디어(Social Media) 등으로 빠르게 유통되고 있습니다.

오늘은 인터넷 뉴스로부터 수집된 데이터를 활용하여 여론을 자동으로 분석 및 동향을 살펴보는 방법에 대하여 소개하겠습니다.



Keyword : News Analysis, Issue Tracking, Topic Modeling



## 2. Analysis

#### 2.1. Data Overview

데이터 : 키워드 "스포츠"에 대한 포털사이트 N사의 뉴스

수집 기간 : 2019년 08월 ~ 2020년 08월, 1년 

수집 내용 : 언론사, 발행일, 뉴스 제목, 뉴스 내용, 뉴스 링크

수집 방법 : 웹 크롤링 (본 포스팅은 웹 크롤링에 대해 다루지 않습니다.)

해당 데이터는 앞서 소개한 여론의 "동향"을 살펴보기 위해 발행일과 뉴스 내용을 중점적으로 수집하였습니다.



#### 2.2. Modeling Issue

수집한 데이터는 여론을 파악하기 위한 어떤 레이블이 존재하지 않기 때문에 기본적으로 지도 학습(Supervised  Learning)이 어렵습니다.

또한, 데이터의 형태가 텍스트(Text)이며, 분석 미션(Mission)은 주어진 텍스트가 어떤 이슈를 포함하고 있는가를 파악하는 것입니다.

따라서 본 프로젝트의 모델은 주어진 텍스트에 대하여 효과적으로 이슈를 파악할 수 있는 잠재 디리클레 할당(Latent Dirichlet Allocation, LDA)을 사용하였습니다.

<img width="80%" src="/images/02_LDA.png"/>

(본 포스팅은 LDA에 대한 자세한 설명을 다루지 않습니다.)



#### 2.3. Modeling

LDA는 비지도학습(Un-Supervised Learning)으로 이루어지며 분석가가 사전에 정의한 주제(Topic)의 개수에 따라 보유하고 있는 텍스트 내에 어떤 이슈가 있는지 파악합니다.

LDA의 분석 결과는 다양한 하이퍼파라미터(Hyperparameter)에 의해 영향을 받지만 이 중 가장 큰 영향을 미치는 요소는 주제의 개수입니다.

따라서 주제의 개수에 따른 LDA의 성능을 정량적으로 평가할 수 있는 지표인 일관성(Coherence)과 혼잡도(Perplexity)에 대한 평가를 진행하여 주제의 개수를 정합니다. 

<img width="100%" src="/images/03_Hyperthesis.png"/>



실험해볼 수 있는 토픽의 개수의 시작과 끝을 지정한 뒤, 자동으로 주제 개수에 따라 일관성과 혼잡도를 계산한 결과 두 지표를 모두 만족하는 주제 개수는 10개로 선정하였습니다.

이렇게 선정된 LDA의 결과는 다음과 같습니다.

<img width="100%" src="/images/04_Topic.png"/>



#### 2.4. Issue Trend

2.3의 결과와 수집한 발행일을 매핑(Mapping)하여 시계열을 반영한 여론의 동향을 분석합니다.

이때, 수집한 발행일과 2.3의 결과를 정리하는 과정이 필요합니다.



먼저, 발행일은 일(Day) 단위로 동향을 분석할 경우, 분석 기준(일)이 미세해지므로 충분한 양의 데이터가 있지 않아 대표성이 떨어지거나 특정 데이터에 의해 결과가 민감해질 수 있습니다.

따라서 일 단위 분석 기준을 월(Month) 단위로 분석 기준을 통합해줍니다.



두 번째로 2.3의 결과는 각 데이터(또는 문서)가 어떤 주제에 할당되는지를 나타낸 결과이며  각 데이터는 다양한 주제를 가질 수 있습니다.

이러한 특징으로 해당 결과를 바로 동향에 적용할 경우, 아주 미세하게 반영된 주제라도 다른 주제와 동등한 영향력을 행사할 수 있습니다.

따라서 해당 데이터가 다양한 주제를 갖더라도 그 영향력이 아주 미세하다면 여론이라 반영하지 않도록 작업이 필요합니다.

이 작업은 분석 기준(월)에 속하는 데이터들에 대해 각 주제별 통계 데이터를 적용하여 처리하였습니다.



이렇게 처리된 결과를 토대로 분석된 결과는 다음과 같습니다.

<img width="100%" src="/images/05_Issue.png"/>



## 3. Conclusion

2.3의 Topic3과 Topic4에 속하는 키워드(Keyword)를 확인하면 코로나, 감염, 확산, 확진, 분리, 방역 등인 것을 확인할 수 있습니다.

해당 데이터는 2019년 08월부터 2020년 08월까지의 뉴스입니다.

따라서 수집된 데이터는 코로나가 발생한 전 후를 모두 포함하고 있는 데이터이므로 코로나와 관련된 주제가 생성된 것을 확인할 수 있습니다.

하지만 같은 주제일 것 같지만 서로 다른 주제로 구성되어 있습니다.

이러한 차이점은 이후 2.4인 동향 분석에서 보다 구체적으로 확인할 수 있습니다.

확진, 분리, 방역, 발생과 같은 키워드가 속한 Topic4는 코로나가 등장한 시기와 같은 3시점(2019년 11월)을 기점으로 급격히 발생하였습니다.

코로나, 감염, 확산, 거리, 두기와 같은 키워드가 속한 Topic3은 코로나가 유행한 시기인 7시점(2020년 3월)을 기점으로 급격히 발생하였습니다.

해당 프로젝트를 통해 스포츠와 관련된 여론에서 코로나가 등장함에 따라 코로나가 스포츠 여론에 큰 영향을 주었음을 확인하였습니다.







Made By Seokeeee and Su-min Seo



