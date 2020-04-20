---
layout: post
title: 'java non-zero exit value 1' error
category: Java
---
요근래 자주 접하는 에러로 발견시 원인을 기록 / IDE - Intellij
```
.jdk/Contents/Home/bin/java'' finished with non-zero exit value 1
```
 - gradle project에서 main/java가 아닌 다른 경로에 Sources를 지정해서 그안의 클래스를 run할때 오류발생.  
  ex) main/example/test.java를 run
