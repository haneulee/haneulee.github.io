---
layout: post
title: "Javascript Context"
date: 2018-09-12 17:56:52 +0900
categories: [Development, Javscript]
tags: [javascript, context]
---

자바스크립트 실행 컨텍스트
===========

: 현재 실행되는 컨텍스트에서 이 컨텍스트와 관련 없는 실행 코드가 실행되면, 새로운 컨텍스트가 생성되어 스택에 들어가고 제어권이 그 컨텍스트로 이동한다. 

- 실행 컨텍스트가 형성되는 경우
    - 전역 코드
    - eval() 함수로 실행되는 코드
    - 함수 안의 코드를 실행하는 경우
