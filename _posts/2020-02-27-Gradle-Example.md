---
layout: post
title: Gradle Example
category: Gradle
---
<!-- 이 예제는 Groovy로 작성되었음.   -->
## 1. [Plugins](https://docs.gradle.org/current/userguide/plugins.html)
### 1.1 Script Plugin
```
apply from: 'other.gradle'
apply plugin: 'testPlugin'
```
<!-- ```
apply(from = "other.gradle.kts")
apply(plugin = "testPlugin")
``` -->

### 1.2 Core Plugin
: Gradle에서 기본적으로 제공하는 플러그인
 - [Core Plugin Reference](https://docs.gradle.org/current/userguide/plugin_reference.html)
```
plugins {
    id 'java'
}
```
<!-- ```
plugins {
  java
}
``` -->

### 1.3 Cummunity Plugin
: Gradle 사용자가 작성한 Custom Plugin (Plugin<T> 인터페이스를 구현 / [참조](https://docs.gradle.org/current/userguide/custom_plugins.html#custom_plugins))
 - [Cummunity Plugins](https://plugins.gradle.org/)
```
plugins {
  id "org.hidetake.swagger.generator" version "2.18.2"
}
```
<!-- ```
plugins {
  id("org.hidetake.swagger.generator") version "2.18.2"
}
``` -->

### 1.4 apply & plugins{}  
 - plugins{}(블럭)은 subprojects, allprojects에서는 동작하지 않는다.
 -

## 2. [Repositories](https://docs.gradle.org/current/userguide/declaring_repositories.html)
### 2.1 공용 저장소 종속성
: 잘 알려진 공용 저장소는 세종류가 있다.  
 - repositories 표현문법은 kotlin과 동일하다.
![publicRepositories](https://sky25o5.github.io/image/publicRepositories.png)

#### 2.1.1 MavenCentral
>[Maven Repository](https://mvnrepository.com/)  
java opensouce library 제공
```
repositories {
    mavenCentral()
}
```  

#### 2.1.2 Jcenter
>[Bintary Jcenter](https://bintray.com/bintray/jcenter)
java opensouce library 제공
```
repositories {
    jcenter()
}
```  

#### 2.1.3 Google
>[Google Maven repository](https://maven.google.com/web/index.html)
Android SDK 및 Android 관련 library 제공
```
repositories {
    google()
}
```  

### 2.2 외부 저장소에 업로드
```
apply plugin: 'java'
apply plugin: 'maven-publish'

group = "com.kotlin.plugins"
version = '0.0.1-SNAPSHOT'
project.group = 'com.kotlin.plugins'
def userId = "admin"
def nexusPassword = "p@ssw0rd"


publishing {
    publications {
        mavenJava(MavenPublication) {
            artifactId "testProject"
            from components.java
        }
    }
}
publishing {
    repositories {
        maven {
            credentials {
                username "${userId}"
                password "${nexusPassword}"
            }
            url "http://localhost:8081/repository/maven-snapshots/"
        }

    }
}
```
<!-- ```
plugins {
    `maven-publish`
    `java`
}
publishing {
    publications {
        create<MavenPublication>("maven") {
            groupId = "com.kotlin.plugins"
            artifactId = "project-sample"
            // artifactId를 지정하지 않을 경우 프로젝트명으로 적용됨.
            version = "1.0-SNAPSHOT"

            from(components["java"])
        }
    }

    repositories {
        maven {
            url = uri("http://localhost:8081/repository/maven-snapshots/")
            credentials {
                username = "admin"
                password = "${nexusPassword}"
            }
        }
    }
}
``` -->

[참조](https://docs.gradle.org/current/userguide/declaring_repositories.html)   
[...](https://docs.gradle.org/current/userguide/dependency_management_for_java_projects.html)

## 3. [Tasks](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)
### 3.1 task 등록
```
task somethingTask {
  doLast {
    println "hello"
  }
}
task testTasK {
  doFirst {
    println "Test First"
  }
  dependsOn somethingTask
  doLast {
    println "Test Last"
  }
}
```
<!-- ```
tasks.register("somethingTask") {
  doLast {
    println("hello")
  }
}
tasks.register("testTasK") {
  doFirst {
    println("Test First")
  }
  dependsOn("somethingTask")
  doLast {
    println("Test Last")    
  }
}
``` -->
dependsOn을 통해 작업 의존성을 설정 할 수 있다.  
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
10.times { counter ->
    task "taskNum$counter" {
      daLast(){
        println "taskNum : $counter"
      }
    }
}
```
<!-- ```
repeat(10) { counter ->
    tasks.register(taskNum$counter) {
      daLast(){
        println("taskNum : $counter")
      }
    }
}
``` -->
times의 값만큼 task가 생성된다. task0, task1 ... task9  

### 3.3 정의된 task에 대한 종속성 추가
```
10.times { counter ->
    task "taskNum$counter" {
        doLast(){
            println "taskNum : $counter"
        }
    }
}
taskNum0.dependsOn taskNum3 taskNum5
```
<!-- ```
repeat(10) { counter ->
    tasks.register("taskNum$counter") {
        doLast(){
            println("taskNum : $counter")
        }
    }
}
tasks.named("taskNum0") { dependsOn("taskNum3", "taskNum5")}
``` -->
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
task a {
    ext.b = "myValue"
}

task printTaskProperties {
    doLast {
        println a.b
    }
}
```
<!-- ```
tasks.register("a") {
    extra["b"] = "myValue"
}

tasks.register("printTaskProperties") {
    doLast {
        println(tasks["a"].extra["b"])
    }
}
``` -->
```
실행
gradle printTaskProperties

> Task :printTaskProperties
myValue
```

### 3.5 기본 task 정의
```
defaultTasks 'cleaner', 'runner'

task cleaner {
    doLast {
        println 'Default Cleaning!'
    }
}

task runner {
    doLast {
        println 'Default Running'!
    }
}

task other {
    doLast {
        println "I'm not a default task!"
    }
}
```
<!-- ```
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
``` -->
```
실행
gradle

> Task :cleaner
Default Cleaning!

> Task :runner
Default Running!
```
defaultTasks가 중복 정의되어 있는 경우, 가장 마지막에 정의된 작업이 적용된다.

### 3.6 외부 종속성 정의 ([의존성](https://docs.gradle.org/current/userguide/declaring_dependencies.html))
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath group: 'commons-codec', name: 'commons-codec', version: '1.2'
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
        classpath group: 'commons-codec', name: 'commons-codec', version: '1.2'
    }
}

task encode {
    doLast {
        def byte[] encodedString = new Base64().encode('hello world\n'.toByteArray())
        println new String(encodedString))
    }
}
```
<!-- ```
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
``` -->
```
실행
gradle encode

> Task :encode
aGVsbG8gd29ybGQK
```

## 4. [dependencies](https://docs.gradle.org/current/userguide/dependency_management_for_java_projects.html )
### dependencies & buildscipt dependencies
dependencies
```
repositories {
    mavenCentral()
}
dependencies {
    implementation group:'commons-codec', name:'commons-codec', version:'1.2')
}
```
<!-- ```
repositories {
    mavenCentral()
}
dependencies {
    "implementation"(group = "commons-codec", name = "commons-codec", version = "1.2")
}
``` -->
buildscript dependencies
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath group:'commons-codec', name:'commons-codec', version:'1.2'
    }
}

task encode {
    doLast {
        def byte[] encodedString = new Base64().encode('hello world\n'.getBytes())
        println new String(encodedString)
    }
}
```
<!-- ```
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
``` -->
dependencies를 정의할때 buildscipt 블럭을 씌운 경우와 아닌 경우가 있다.  
둘의 차이는 buildscipt 블럭이 없는경우는 컴파일에서만 사용되는 경우이고,
반대의 경우는 task에서 dependencies에 정의된 lib의 클래스를 사용해야되는 경우이다.  
[참조](https://notpeelbean.tistory.com/entry/gradle-buildscript-dependencies-%EC%99%80-dependencies-%EC%9D%98-%EC%B0%A8%EC%9D%B4)
