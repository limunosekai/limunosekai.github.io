---
layout: post
title: Java Day 2
category: Java
permalink: /java/:year/:month/:day/:title/
tags: [java]
comments: true
---

---

## JAVA Day 2

---

1. 식별자

   - 식별자란?
     - 구성요소를 구별하는 이름
     - 클래스명, 매서드명, 변수명과 같이 서로를 구별하는 이름
   - 식별자명을 정의하는 규칙
     - 첫 글자로 공백, 특수문자, 숫자 사용 X
     - '\_', '$' 는 사용 할 수 있으나 라이브러리와 겹쳐서 오류를 발생 할 수 있으므로 자제
     - 예약어 사용 X
     - 한글 사용 가능하나 쓰지말자
     - 영어 대소문자 구별한다
   - 클래스명을 정의하는 규칙
     - 첫 글자는 항상 대문자로
     - Camel 규칙에 의거해서 작성
     - 클래스명은 반드시 파일명과 같아야한다

2. 주석

   - // : 단행 주석처리, 해당 행만
   - /\* \*/ : 다행 주석 처리, 범위 주석
   - /\*\* \*/ : javadoc 주석처리, API document 생성시 사용
   - Cleancode를 위해 주석을 많이 활용하자 !

3. 변수

   - 변수란?
     - 하나의 값을 저장 할 수 있는 메모리 공간
   - 변수의 선언

   ```java
   int age; // 정수를 저장 할 수 있는 age 변수 선언
   age = 30; // 변수의 초기화
   int age = 30; // 선언과 초기화 동시에 가능

   //초기화 되지 않은 변수는 사용 할 수 없다
   int value;
   int result = value + 10; // 선언만 되고 초기화 되지 않아서 compile error 발생

   //변수의 값은 바뀔 수 있다
   int age = 30;
   age = 19; // 가장 마지막에 저장된 값이 변수의 값이 됨

   //변수의 범위
   int a = 10; // 전역변수, 다른 곳에서 사용 가능
   if() {
       int b = 10; //if블록 안에서 생성됨, 지역변수로 블록 밖에서 사용 X
   }
   //초기화시 자료의 표현범위를 넘을 수 없다
   byte b = 127; //byte의 범위는 -128~127
   b++;
   System.out.println(b); // -128 출력, 윤회사상으로 127의 다음 수인 -128로 돌아감
   ```

   - 기본 자료형

     - 논리형 : boolean, 1byte, (true, false)

     - 정수형

       1. byte : 1byte, -128~127 (색상 정보, 이미지,파일의 이진 데이터 처리시 사용)

       2. char : 2byte, 0~65535 (유니코드 문자 표기), 문자형으로 분류하기도

          ```java
          char ch = ' '; //''와 같이 빈 문자를 대입하면 compile error 발생
          String str = ""; //String은 빈 문자로 초기화 할 수 있다.

          char ch1 = 'A'; //아스키코드값 65
          char ch2 = '\u0041'; //A의 유니코드

          System.out.println(ch1+ch2); //AA 출력
          System.out.println((ch1+ch2)); //130 출력, 문자는 연산이 가능하면 연산을 수행함
          System.out.println((char)(ch1+ch2)); // z 출력, 해당 값의 문자 출력
          ```

       3. short : 2byte, -32,768~32,767 (C언어와의 호환을 위해 사용)

       4. int : 4byte, 대략 -21억4천만~21억4천만, 정수형의 기본 자료형

       5. long : 8byte, -2^63~2^63-1 (은행, 우주 등 수치가 큰 데이터 처리시 사용)

          ```java
          long A = 1111L; //long타입의 변수는 초기화시 l or L을 붙여야함
          ```

     - 실수형

       1. float : 4yte, -3.4E38 ~ 3.4E38

          ```java
          float f = 3.14f; //float타입의 변수는 초기화시 f or F를 붙여야함
          ```

       2. double : 8byte, -1.7E308 ~ 1.7E308, 실수형의 기본 자료형

   - 참조 자료형

     - 기본 자료형을 제외한 모든 타입으로 Stirng도 포함
     - new 연산자를 이용하여 메모리 할당
     - 클래스 기반 타입

   - Wrapper Class

     - 기본 자료형을 객체로 다루기 위해 사용하는 클래스

     - 기본자료형의 첫글자를 대문자로 변환 (Integer, Character 예외)

       ```java
       Integer num = 15; //Auto Boxing
       int num2 = num; //Auto Unboxing
       ```

   - 형변환

     - 프로모션 : 더 큰 자료형으로 변환(자동)

       ```java
       short a = 10;
       short b = 10;
       int c = a + b; // 자동 타입 변환이 일어남
       ```

     - 디모션 : 작은 자료형으로 변환(명시)

       ```java
       int intValue = 1234;
       byte byteValue = (byte)intValue; //-46출력, 강제 타입변환을 캐스팅이라 한다, 쓰레기값이 나오지 않도록 주의
       ```
