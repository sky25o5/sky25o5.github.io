---
layout: post
title: Gradle Example
category: Gradle
---
<!-- 이 예제는 Kotlin으로 작성되었음.   -->
## 1. [Plugins](https://docs.gradle.org/current/userguide/plugins.html)
### 1.1 Script Plugin
```
apply(from = "other.gradle.kts")
```

### 1.2 Core Plugin
: Gradle에서 기본적으로 제공하는 플러그인
 - [Core Plugin Reference](https://docs.gradle.org/current/userguide/plugin_reference.html)
```
plugins {
  java
}
```

### 1.3 Cummunity Plugin
: Gradle 사용자가 작성한 Custom Plugin (Plugin<T> 인터페이스를 구현)
 - [Cummunity Plugins](https://plugins.gradle.org/)
```
plugins {
  id("org.hidetake.swagger.generator") version "2.18.2"
}
```

## 2. [Repositories](https://docs.gradle.org/current/userguide/declaring_repositories.html)
[자료 정리중](https://docs.gradle.org/current/userguide/dependency_management_for_java_projects.html)

## 3. [Tasks](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)
### 3.1 task 등록
```
tasks.register("somthingTask") {
  doLast {
    println("hello")
  }
}
tasks.register("testTasK") {
  doFirst {
    println("Test First")
  }
  dependsOn("somthingTask")
  doLast {
    println("Test Last")    
  }
}
```
dependsOn()을 통해 작업 의존성을 설정 할 수 있다.  
```
실행
gradle testTasK

> Task :somthingTask  
hello  
> Task :testTasK  
Test First  
Test Last
```

### 3.2 dynamic tasks
```
repeat(10) { counter ->
    tasks.register(taskNum$counter) {
      daLast(){
        println("taskNum : $counter")
      }
    }
}
```
repaet의 값만큼 task가 생성된다. task0, task1 ... task9  

### 3.3 정의된 task에 대한 종속성 추가
```
repeat(10) { counter ->
    tasks.register("taskNum$counter") {
        doLast(){
            println("taskNum : $counter")
        }
    }
}
tasks.named("taskNum0") { dependsOn("taskNum3", "taskNum5")}
```
```
실행
gradle taskNum0

> Task :taskNum3
taskNum : 3

> Task :taskNum5
taskNum : 5

> Task :taskNum0
taskNum : 0
```

### 3.4 task에 속성 추가
```
tasks.register("a") {
    extra["b"] = "myValue"
}

tasks.register("printTaskProperties") {
    doLast {
        println(tasks["a"].extra["b"])
    }
}
```
```
실행
gradle printTaskProperties

> Task :printTaskProperties
myValue
```

### 3.5 기본 task 정의
```
defaultTasks("cleaner", "runner")

task("cleaner") {
    doLast {
        println("Default Cleaning!")
    }
}

tasks.register("runner") {
    doLast {
        println("Default Running!")
    }
}

tasks.register("other") {
    doLast {
        println("I'm not a default task!")
    }
}
```
```
실행
gradle

> Task :cleaner
Default Cleaning!

> Task :runner
Default Running!
```
defaultTasks()가 중복 정의되어 있는 경우, 가장 마지막에 정의된 작업이 적용된다.

### 3.6 외부 종속성 정의
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        "classpath"(group = "commons-codec", name = "commons-codec", version = "1.2")
    }
}
```
#### 3.6.1 정의된 클래스 task에서 사용
```
import org.apache.commons.codec.binary.Base64

buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        "classpath"(group = "commons-codec", name = "commons-codec", version = "1.2")
    }
}

tasks.register("encode") {
    doLast {
        val encodedString = Base64().encode("hello world\n".toByteArray())
        println(String(encodedString))
    }
}
```
```
실행
gradle encode

> Task :encode
aGVsbG8gd29ybGQK
```
