---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - typescript
  - express
  - 設計
  - オブジェクト指向
  - DI
  - 3層アーキテクチャ
  - デザインパターン
aliases:
  - <% tp.file.title %>
---

## openapi.yml の修正

/api/games のサンプルを修正

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
                        dardMomoveCount:
                          type: integer
                          example: 1,
                        lightMomoveCount:
                          type: integer
                          example: 1,
                        winnerDisc:
                          type: integer
                          example: 1,
                        startedAt:
                          type: string
                          example: 2024-01-01T00:00:00Z
                        endAt:
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
