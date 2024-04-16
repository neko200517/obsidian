## プロジェクトの作成

https://start.spring.io/
でSpringBootの新規プロジェクトの作成

## エントリポイント

```
cd java/com/example/todo 
touch TodoApplication.java
```

```java
// java/com/example/todo/TodoApplication.java 

package com.example.todo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class TodoApplication {

	public static void main(String[] args) {
		SpringApplication.run(TodoApplication.class, args);
	}

}
```

## コントローラー

```bash
mkdir controller 
touch IndexController.java
```

```java
// java/com/example/todo/controller/IndexController.java

package com.example.todo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller("/")
public class IndexController {
  @GetMapping
  @ResponseBody
  public String index() {
    return "Hello, World";
  }
}
```

## デバッグで起動

VSCodeの機能で以下のエントリポイントで表示されるDebugボタンを押下する。
その後Springの起動画面が表示されるのでブラウザでlocalhost:8080にアクセスすると画面が表示される。

```java
public static void main(String[] args)
```
