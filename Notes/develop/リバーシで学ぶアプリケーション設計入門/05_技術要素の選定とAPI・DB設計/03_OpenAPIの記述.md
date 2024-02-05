## OpenAPIとは

- OpenAPIはWebAPIの定義を記述するための仕様
- WebAPIの定義を独自の形式ではなくOpenAPIの形式で書けば様々なツールで読み込んで活用することができる

## VSCodeに拡張機能を入れる

Swagger Viewerを導入する

## ymlファイルを作成する

```bash
touch openapi.yaml
```

APIの設計で作成したメモを参考にOpenAPI形式のymlファイルを作成する。
パスの名前を選択した状態でAlt+Shift+Pを押下するとSwagger Viewerが起動する。

### メタ情報を書く

```yml
openapi: '3.0.3'

info:
  title: udemy-reversi-webapp
  version: 1.0.0
```

### /api/games

```yml
paths:
  /api/games:
    get:
      responses:
        200:
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  games:
                    type: array
                    items:
                      type: object
                      properties:
                        id:
                          type: integer
                          example: 1,
                        winnerDisc:
                          type: integer
                          example: 1,
                        startedAt:
                          type: string
                          example: 2024-01-01T00:00:00Z
    post:
      responses:
        201:
          description: Created
```

### /api/games/latest/turns/{turnCount}

```yml
  /api/games/latest/turns/{turnCount}:
    parameters:
      - name: turnCount
        in: path
        schema:
          type: integer
        required: true
    get:
      responses:
        200:
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  typeCount:
                    type: integer
                    example: 1
                  board:
                    type: array
                    example:
                      [
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 1, 2, 0, 0, 0],
                        [0, 0, 0, 2, 1, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                      ]
                  nextDisc:
                    type: integer
                    example: 1
                  winnerDisc:
                    type: integer
                    example: 1
```

### /api/games/latest/turns

```yml
  /api/games/latest/turns:
    post:
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                turnCount:
                  type: integer
                  example: 1
                move:
                  type: object
                  properties:
                    disc:
                      type: integer
                      example: 1
                    x:
                      type: integer
                      example: 0
                    y:
                      type: integer
                      example: 0
      responses:
        201:
          description: Created
```

## 全体

```yml
openapi: '3.0.3'

info:
  title: udemy-reversi-webapp
  version: 1.0.0

paths:
  /api/games:
    get:
      responses:
        200:
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  games:
                    type: array
                    items:
                      type: object
                      properties:
                        id:
                          type: integer
                          example: 1,
                        winnerDisc:
                          type: integer
                          example: 1,
                        startedAt:
                          type: string
                          example: 2024-01-01T00:00:00Z
    post:
      responses:
        201:
          description: Created

  /api/games/latest/turns/{turnCount}:
    parameters:
      - name: turnCount
        in: path
        schema:
          type: integer
        required: true
    get:
      responses:
        200:
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  typeCount:
                    type: integer
                    example: 1
                  board:
                    type: array
                    example:
                      [
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 1, 2, 0, 0, 0],
                        [0, 0, 0, 2, 1, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                        [0, 0, 0, 0, 0, 0, 0, 0],
                      ]
                  nextDisc:
                    type: integer
                    example: 1
                  winnerDisc:
                    type: integer
                    example: 1

  /api/games/latest/turns:
    post:
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                turnCount:
                  type: integer
                  example: 1
                move:
                  type: object
                  properties:
                    disc:
                      type: integer
                      example: 1
                    x:
                      type: integer
                      example: 0
                    y:
                      type: integer
                      example: 0
      responses:
        201:
          description: Created
```
