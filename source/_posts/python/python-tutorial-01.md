---
title: Python 스터디 - 01(설치 및 Terminal에서 사용)
date: 2017-09-30 17:38:57
tags:
- Python
categories:
- Python
---

## **파이썬 스터디 - 01(설치 및 Terminal에서 사용해보기)**

## STEP 1. Mac Os에서 Python 설치하기
https://www.python.org/downloads/
페이지 방문

![Python](/images/python/python.png)

3.xx Version 다운로드

설치 파일을 눌러, 계속, 동의 해서 설치를 진행합니다.

따로 설정해주어야 할 것이 없음.

기본적으로 Python이 Mac에 설치되어 있지만, 3버전을 위해서 별도로 새로 다운로드를 진행합니다.

별 일 없으면, 정상적으로 설치가 완료되었을 것입니다.

정상 설치된 것을 체크하려면 Terminal에서

```bash
$ python3 --version
```
아래와 같이, 버전이 출력된다면 정상적으로 설치가 된 것입니다.

![Python3](/images/python//python3.png)

## STEP 2. Terminal에서 간단한 문법 사용해보기

Terminal에서 python3 입력

10+11 등을 치고 바로 엔터를 치면
바로 결과값이 나온다.

인터프리터의 언어의 특징 컴파일 필요없이 입력한 코드가 바로 실행된다.

![terminal](/images/python//terminal.png)

아래와 같이 나눗셈, 곱셈이 적용되며
a=10처럼 변수를 지정해줄 수 있으며 print를 이용해 출력해 줄 수 있다.

![tutorial](/images/python//tutorial.png)

if문을 한번 사용해 보자,
a=100 값을 준 뒤에

if a>90을 입력하고 :을 입력해서 다음줄 까지 계속 입력하도록 할 수 있다.
그런데 그냥 출력을 했을 경우 아래처럼 indented block이라는 에러가 발생한다.

![tutorial](/images/python//if.png)

python에서는 indent(들여쓰기)를 통해 if 블럭을 구분하므로 if 사이에 들어갈 내용은 아래와 같이
탭 키를 한번 눌러서 들여쓰기를 한 후에 코드를 작성해야 한다.
...인 상태에서 엔터를 한번 더 누르면 결과가 출력된다.

![tutorial](/images/python//if2.png)
