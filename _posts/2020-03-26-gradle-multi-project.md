---
layout: post
category: Gradle
title: intellij에서 gradle multi project 구성
---  

### 1. 메인 프로젝트에 복수의 서브프로젝트 구성  
메인 프로젝트 A에 서브 프로젝트 b, c, d의 구성  
[ b, c, d 프로젝트의 의존성 (b <- c <- d) ]  
A:settings.gradle
 ```
 rootProject.name = 'A'
 include 'b', 'c', 'd'
 ```
A:build.Gradle  
 ```
  plugins {
    id 'java'
    id 'java-library'
  }

  group 'com.sky.integration'
  version '1.0.0'

  sourceCompatibility = 1.8

  subprojects {
      apply plugin: 'java'
      task initSourceFolder {
          sourceSets*.java.srcDirs*.each {
              if(!it.exists()) {
                  it.mkdirs()
              }
          }
          sourceSets*.resources.srcDirs*.each {
              if(!it.exists()) {
                  it.mkdirs()
              }
          }
      }
  }
  /* 메인 프로젝트에서 api 적용시 에러가 발생. 서브 프로젝트에서 dependencies 설정
  project(':c') {
      dependencies {
          api project(':b')
      }
  }

  project(':d') {
      dependencies {
          implementation project(':c')
      }
  }*/
 ```
 c:build.gradle  
 ```
  plugins {
    id 'java'
    id 'java-library'
  }

  version '1.0-SNAPSHOT'

  sourceCompatibility = 1.8

  dependencies {
    implementation project(':b')
  }
 ```
 d:build.gradle  
 ```
 plugins {
   id 'java'
   id 'java-library'
 }

 version '1.0-SNAPSHOT'

 sourceCompatibility = 1.8

 dependencies {
   implementation project(':c')
 }
 ```
### 2. 개별 프로젝트를 통합  
a, b, c(메인 프로젝트) 세개의 개별 프로젝트가 있을때의 구성  
  [ a, b, c의 의존성 (a <- b <- c) ]  
 b:settings.gradle  
 ```
 include ':a'
 project(':a').projectDir = new File('../a')  
 // b프로젝트에 대한 a프로젝트의 상대경로  
 ```
 b:build.gradle  
 ```
 dependencies {
    implementation project(path: ':a')
 }
 ```
 c:settings.gradle  
 ```
 include ':a'
 project(':a').projectDir = new File('../a')  
 // c프로젝트에 대한 a프로젝트의 상대경로  
 include ':b'
 project(':b').projectDir = new File('../b')  
 // c프로젝트에 대한 b프로젝트의 상대경로
 ```
 c:build.gradle  
 ```
 dependencies {
    implementation project(path: ':b')
 }
 ```
