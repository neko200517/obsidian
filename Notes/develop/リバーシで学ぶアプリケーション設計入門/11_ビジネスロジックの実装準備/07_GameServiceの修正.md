## Boardクラスの修正 

初期化するためのBoardインスタンスを簡単に生成できるようにする。

```ts
// src/domain/board.ts

// 中略

const E = Disc.Empty;
const D = Disc.Dark;
const L = Disc.Light;

const INITIAL_DISCS = [
  [E, E, E, E, E, E, E, E],
  [E, E, E, E, E, E, E, E],
  [E, E, E, E, E, E, E, E],
  [E, E, E, D, L, E, E, E],
  [E, E, E, L, D, E, E, E],
  [E, E, E, E, E, E, E, E],
  [E, E, E, E, E, E, E, E],
  [E, E, E, E, E, E, E, E],
];

export const initialBoard = new Board(INITIAL_DISCS);
```

## Turnクラスの修正 

最初のターンのインスタンスを簡単に生成できるようにする。

```ts
// src/domain/turn.ts

// 中略

// ゲームの最初のターンを登録
export const firstTurn = (gameId: number, endAt: Date) => {
  return new Turn(gameId, 0, Disc.Dark, undefined, initialBoard, endAt);
};

```

## GameServiceクラスの修正

最初のターンの登録をturnRepositoryで実装。

```ts
import { connectMySql } from '../dataaccess/connection';

import { GameGateway } from '../dataaccess/gameGateway';
import { firstTurn } from '../domian/turn';
import { TurnRepository } from '../domian/turnRepository';

const gameGateway = new GameGateway();
const turnRepository = new TurnRepository();

export class GameService {
  async startNewGame() {
    const now = new Date();
    const conn = await connectMySql();

    try {
      await conn.beginTransaction();

      // ゲームを保存
      const gameRecord = await gameGateway.insert(conn, now);

      // ゲームの最初のターンを登録
      const turn = firstTurn(gameRecord.id, now);
      await turnRepository.save(conn, turn);

      // コミット
      await conn.commit();
    } catch {
      await conn.rollback();
      throw new Error();
    } finally {
      await conn.end();
    }
  }
}
```