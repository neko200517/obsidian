## static/index.html

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="style.css" />
    <title>リバーシ</title>
  </head>
  <body>
    <header>
      <h1>リバーシで学ぶアプリケーション設計入門</h1>
    </header>
    <main>
      <a href="/game/">対戦する</a>
      <table>
        <thead>
          <tr>
            <th>黒を打った回数</th>
            <th>白を打った回数</th>
            <th>勝った石</th>
            <th>対戦開始時刻</th>
            <th>対戦終了時刻</th>
          </tr>
        </thead>
        <tbody id="games-table-body"></tbody>
      </table>
    </main>
    <script src="main.js"></script>
  </body>
</html>
```

## static/main.js 

```js
const gamesTableBodyElement = document.getElementById('games-table-body');

const showGames = async () => {
  const response = await fetch('/api/games');
  const responseBody = await response.json();
  const games = responseBody.games;

  // 事前に全要素を削除
  while (gamesTableBodyElement.firstChild) {
    gamesTableBodyElement.removeChild(gamesTableBodyElement.firstChild);
  }

  games.forEach((game) => {
    const trElement = document.createElement('tr');

    const appnedTdElement = (innerText) => {
      const tdElement = document.createElement('td');
      tdElement.innerText = innerText;
      trElement.appendChild(tdElement);
    };

    appnedTdElement(game.darkMoveCount);
    appnedTdElement(game.lightMoveCount);
    appnedTdElement(game.winnerDisc);
    appnedTdElement(game.startedAt);
    appnedTdElement(game.endAt);

    gamesTableBodyElement.appendChild(trElement);
  });
};

const main = async () => {
  await showGames();
};

main();
```