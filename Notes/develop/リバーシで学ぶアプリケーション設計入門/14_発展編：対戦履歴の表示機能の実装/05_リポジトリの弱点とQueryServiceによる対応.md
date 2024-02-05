## リポジトリを利用したあまり良くない実装 

- それぞれのリポジトリを介してTypeScriptで紐づけを行っている
- SQLでJOINを実行するのと比較してレスポンス面で不利

```ts
import { GameRepository } from '../../domian/model/game/gameRepository';
import { GameResultRepository } from '../../domian/model/gameResult/gameResultRepository';
import { Disc } from '../../domian/model/turn/disc';
import { TurnRepository } from '../../domian/model/turn/turnRepository';
import { connectMySql } from '../../infrastructure/connection';

export class FindLastGamesUseCase {
  constructor(
    private _gameRepository: GameRepository,
    private _gameResultRepository: GameResultRepository,
    private _turnRepository: TurnRepository
  ) {}

  async run(): Promise<any> {
    const conn = await connectMySql();
    try {
      const findCount = 10;

      // 対戦一覧を取得
      const games = await this._gameRepository.findLast(conn, findCount);
      const gameIds = games.map((game) => game.id);

      // 対戦IDに紐づく全ての対戦結果を取得する
      const gameResults = await this._gameResultRepository.findForGameIds(
        conn,
        gameIds
      );

      // 対戦IDに紐づく全てのターンを取得する
      const turns = await this._turnRepository.findForGameIds(conn, gameIds);

      // 対戦結果一覧を結果を格納
      const outputGames = games.map((game) => {
        // 対戦と対応する対戦結果を検索
        const gameResult = gameResults.filter(
          (gameResult) => gameResult.gameId === game.id
        );

        // 対戦と対応するターン一覧を取得
        const turnsForGame = turns.fileter((turn) => turn.gameId === game.id);

        // 黒の打った数を取得
        const darkMoveCount = turnsForGame.fileter(
          (turn) => turn.move.disc === Disc.Dark
        ).length;

        // 白の打った数を取得
        const lightMoveCount = turnsForGame.fileter(
          (turn) => turn.move.disc === Disc.Light
        ).length;

        return {
          gameId: game.id,
          darkMoveCount,
          lightMoveCount,
          startedAt: game.startedAt,
          endAt: gameResult.endAt,
        };
      });

      return { games: outputGames };
    } catch {
      conn.end();
    }
  }
}
```

## 改善案

Repositoryを介さずに直接SQLを実装するルートを追加する

![[Pasted image 20240206010014.png]]