---
title: "The Book of OHDSI Korea"
author: "Seng Chan You"
date: "2019-05-26"
site: bookdown::bookdown_site
output: bookdown::gitbook
documentclass: book
bibliography: [book.bib, packages.bib]
biblio-style: apalike
link-citations: yes
github-repo: OHDSI-KOREA/TheBookOfOhdsiKorea
description: "The Book of OHDSI Korea"
---

# Introduction


최근 전세계적으로 의료 빅데이터 활용에 대한 논의가 뜨겁다.보건 빅데이터를 이용하여 질병 예방에 따른 의료비 절감, 의료기관의 운영비용 절감, 오류에 따른 손실비용 절감 등의 경제적 효과도 기대되어 활용이 증대되고 있다. 최근 헬스케어 서비스를 통해 생산되는 건강정보 관리와 활용에 대한 논의가 활발히 진행되고 있으며, 의료 빅데이터에 대한 니즈와 관련 데이터, 대응 방향 등이 이슈화되고 있다. 

국내에서 의료 빅데이터의 활용이 본격적으로 대두된 것은 건강보험심사평가원 (이하 심평원)에서 보건의료빅데이터 시스템을 연구자들에게 개방하면였다. 이후 많은 국내 의료 연구자들이 국민 건강 보험 빅데이터 연구를 진행하여 세계적인 연구 성과를 다수 발표 하고 있다. 심평원은 전국민 기반의 실제 임상 데이터 (Real World Data)를 개인 단위의 세부 의료이용 데이터를 포함하여 관리하고 있으며, 이를 모두 표준화된 청구 서식을 기반으로 수집하고 있다. 하지만, 임상 정보 부족 및 비급여 데이터 부재 등의 한계 등이 지속적으로 지적받고 있고, 데이터 수집의 목적이 심사 업무를 수행하기 위함이다보니, 의료 연구를 위해서는 복잡한 재처리 과정이 필요했다. 또한, 국제 표준에 따른 용어 및 구조 표준을 채택한 것이 아니기 때문에, 타국가 데이터 또는 임상 데이터와의 연계 및 융합에 어려움이 있었다. 철저한 보안을 이용하여 개인 정보 유출을 방지하고 있지만, 보건 빅데이터의 특성상 민감 정보에 대한 유출 가능성 역시 지속적으로 제기되고 있다. 
