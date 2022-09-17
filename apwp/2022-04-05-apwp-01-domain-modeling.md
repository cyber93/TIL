---
layout: post
title: Architecture Patterns with Python - Chap01. 도메인 모델링
category: python
tags: [python, 파이썬, 아키텍처]
comments: true
---

# APWP - Chap01. 도메인 모델링
> [Github Book & Code](https://github.com/cosmicpython)

> [Cosmic Python - Chap01. Domain Modeling](https://www.cosmicpython.com/book/chapter_01_domain_model.html)

![screenshot](../../assets/post-img/python/apwp.png)

## Part 1. 도메인 모델링을 지원하는 아키텍처 구축
대부분의 개발자가 새로운 시스템을 설계하는 요청을 받으면, 즉시 데이터베이스 스키마를 그리기 시작하고, 그 다음에 객체 모델을 생각한다. 여기서부터 모든 것이 잘못되기 시작한다.
`→ 먼저 행동하고(객체를 만들고) 저장에 대한 요구 사항은 행동에 맞춰 정해져야 한다.`

Chap1에서 TDD를 통해 풍부한 `객체 모델을 만드는 방법`을 살펴보고, 그 후 이 모델을 `기술적인 고려 사항`으로부터 분리해 유지하는 방법을 살펴 본다.

적극적으로 리팩토링할 수 있도록 영속성(Persistence)을 모르는 코드를 만드는 방법과(`저장소 패턴`) 도메인 주변에 안정적인 API를 만드는 방법을(`서비스 계층 패턴, 작업 단위 패턴, 애그리게이트 패턴`) 보여 준다.<br>
▶ 저장소 패턴(Repository Pattern) → 영속적인 저장소에 대한 추상화<br>
▶ 서비스 계층 패턴(Service Layer Pattern) → 유스케이스의 시작과 끝을 명확하게 정의<br>
▶ 작업 단위 패턴(Unit of Work Pattern) → 원자적 연산을 제공<br>
▶ 애그리게이트 패턴(Aggreate Pattern) → 데이터 정합성을 강화<br>

`※ 모든 Chapter는 언제나 그랬듯이 테스트를 만드는 것부터 시작 → TDD(Test-Driven Development)`

## 비지니스 프로세스를 코드로 모델링하는 방벙 - 도메인 모델링
3-Layer Architecture가 일반적 → 비지니스 로직 계층이 핵심, 이 책에서는 도메인 모델이라고 함


## (이 책에서) 가구 판매 회사인 메이드닷컴의 도메인 모델


## 도메인 모델링 Key Factors - 값 객체(Value Object)


## 도메인 모델링 Key Factors - 엔티치(Entity)


## 도메인 모델링 Key Factors - 도메인 서비스
