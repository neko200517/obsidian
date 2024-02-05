## 概要

アプリケーション層⇒インフラストラクチャ層に依存していたものを修正。
プレゼンテーション層から依存性を注入して依存関係を分離させる。

## GameServiceの修正

コンストラクタでインタフェースを受け取るように実装

```ts
// 中略
import { GameRepository } from '../../domian/model/game/gameRepository';
import { TurnRepository } from '../../domian/model/turn/turnRepository';

export class GameService {
  constructor(
    private _gameRepository: GameRepository,
    private _turnRepository: TurnRepository
  ) {}
  // 中略
}
```

## GameRouterの修正

依存性を注入する

```ts
// 中略
import { GameMySQLRepository } from '../infrastructure/repsitory/game/gameMySQLRepository';
import { TurnMySQLRepository } from '../infrastructure/repsitory/turn/turnMySQLRepository';

export const gameRouter = express.Router();

const gameService = new GameService(
  new GameMySQLRepository(),
  new TurnMySQLRepository()
);
// 中略
```

## TurnServiceの修正 

コンストラクタでインタフェースを受け取るように実装

```ts
// 中略
import { TurnRepository } from '../../domian/model/turn/turnRepository';
import { GameRepository } from '../../domian/model/game/gameRepository';
import { GameResultRepository } from '../../domian/model/gameResult/gameResultRepository';

export class TurnService {
  constructor(
    private _turnRepository: TurnRepository,
    private _gameRepository: GameRepository,
    private _gameResultRepositry: GameResultRepository
  ) {}
// 中略
}
```

## TurnRouterの修正 

依存性の注入する

```ts
// 中略
import { TurnMySQLRepository } from '../infrastructure/repsitory/turn/turnMySQLRepository';
import { GameMySQLRepository } from '../infrastructure/repsitory/game/gameMySQLRepository';
import { GameResultMySQLRepository } from '../infrastructure/repsitory/gameResult/gameResultMySQLRepository';

// 中略

const turnService = new TurnService(
  new TurnMySQLRepository(),
  new GameMySQLRepository(),
  new GameResultMySQLRepository()
);
// 中略
```
