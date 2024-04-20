## build.gradle

H2 DatabaseとMyBatisの依存関係を追加

```json
dependencies {
    // 中略
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.2'
	runtimeOnly 'com.h2database:h2'
    testImplementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter-test:3.0.2'
    // 中略
}
```

## resources/application.properties

設定ファイルに以下の内容を記述する

```json
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:todo
spring.datasource.username=sa
spring.datasource.passowrd=
spring.sql.init.encoding=UTF-8
```

## H2データベースにアクセス

ブラウザにhttp://localhost:8080/h2-consoleと入力し、接続画面にアクセス。
ドライバ設定とURL、ユーザー名、パスワードを上記のものに書き換える。