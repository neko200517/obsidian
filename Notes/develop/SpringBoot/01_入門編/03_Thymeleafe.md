## build.gradleに依存関係を追加

```json
// 中略
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	// 中略
}
```

## Controllerを修正　

```java
// java/com/example/todo/controller/IndexController.java

package com.example.todo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller("/")
public class IndexController {
  @GetMapping
  public String index() {
    return "index";
  }
}
```

## resources/templats/index.htmlを作成

```bash
mkdir java/resources/templates
touch java/resources/templates/index.html
```

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Title</title>
  </head>
  <body>
    <h1>Hello, world! from Tymeleaf</h1>
  </body>
</html>
```

再読込することで自動でindex.htmlを読み込んでくれる

## Tymeleafでのコメントの書き方

```html
<!--/* コメント */-->

<!--/*-->
複数行コメント
複数行コメント
<!--*/-->
```
