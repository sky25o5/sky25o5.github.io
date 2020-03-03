---
layout: post
title: Gradle Example
category: Gradle
---
이 예제는 Groovy로 작성되었음.  
## Plugins
### Core Plugin
: Gradle에서 기본적으로 제공하는 플러그인
 - <a href="https://docs.gradle.org/current/userguide/plugin_reference.html">Core Plugin Reference</a>
```
plugins {
  id 'java'  
}
```

### Cummunity Plugin
: Gradle 사용자가 작성한 Custom Plugin
 - <a href="https://plugins.gradle.org/"> Cummunity Plugins</a>
```
plugins {
  id "org.hidetake.swagger.generator" version "2.18.2"
}
```

## Tasks  
