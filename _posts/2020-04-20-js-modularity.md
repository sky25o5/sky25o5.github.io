---
layout: post
title: java script modularity
category: JS
---
- js를 모듈화하는 과정의 시행착오를 기록  

## java script의 import / export
- 예젤로 작성된 js 파일명과 함수명은 임의로 작성.
### export
- 외부에서 사용될 js는 다음과 같이 export를 해주어야 한다.  
> ref.js  
```
function hello() {
  alert("hello");
}
export {hello};
```

### import
- export로 지정된 js의 함수를 사용하려면 import로 지정한다.  
> scriptImport.js  
```
function btnclick() {
  hello();
}
import {hello} from "./ref.js";
```

### html에서의 사용
> jstest.html
{% raw %}
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title></title>
    <script type="module">
      import {hello} from "./ref.js";
      window.test=function() {
        hello();
      }
    </script>
  </head>
  <body>
    <button type="button" id="btnHello" onclick="window.test()">button</button>
  </body>
</html>
```
{% endraw %}
- import를 사용한 js의 함수를 html에서 호출하려면 window 객체에 등록해주어야 된다.  
script type이 "module"로 지정되면 scirpt가 load될때만 유효하기 때문이다.
