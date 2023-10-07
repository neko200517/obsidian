## CustomException を作成する

FastAPIにはデフォルトで HtmlException が用意されているが、自由度の効く Json 形式でエラーを返したい場合、以下のように CustomException を作成する。

```python
# crud.py

class CustomException(Exception):
    def __init__(
        self,
        error_code: int = error_code.NETWORK_ERROR,
        message: str = "Network Error",
        log: str = "",
    ):
        self.error_code = error_code
        self.message = message
        self.log = log
```

## exception_handler

exception_handler アノテーションを利用することですべての例外をフックして送信元にエラーレスポンスを返すことができる。

```python
# main.py

@app.exception_handler(CustomException)
async def custom_exception_handler(request: Request, exc: CustomException):
    return JSONResponse(
        status_code=500,
        content={"error_code": exc.error_code, "message": exc.message, "log": exc.log},
    )
```

## try～catch

以上の用意ができたので crud 操作時に例外処理を実装する。
エラーが起きたときに以下の要件でレスポンスを返すように実装する。

- エラーコードを返す 
- エラーメッセージを返す 
- 直前のログを返す 

### エラー情報の定義ファイルを作成する

サンプル：ユーザー作成時の例外とエラーメッセージ

```python
# error_code.py

ERROR_CREATE_USER = 100

ERROR_MESSAGES = {}
ERROR_MESSAGES[ERROR_CREATE_USER] = "ユーザー作成時にエラーが起こりました。"

# 中略
```

### traceback で直前のログを取得する

最新の実行ログ を一つだけ取得する。

```python
import traceback

# 中略
traceback.format_exception_only(type(e), e)[0],
```

### 実装 

サンプル：ユーザー作成と例外処理の実装例。

```python
# crud.py

def create_user(db: Session, user: schemas.UserCreate):
    db_user = None
    try:
        db_user = models.User(username=user.username)
        db.add(db_user)
        db.commit()
        db.refresh(db_user)
    except SQLAlchemyError as e:
        db.rollback()
        raise CustomException(
            error_code=error_code.ERROR_DUPULICATE_USER,
            message=error_code.ERROR_MESSAGES[error_code.ERROR_DUPULICATE_USER],
            log=traceback.format_exception_only(type(e), e)[0],
        )
    finally:
        db.close()

    return db_user
```

## テストする

API 送信元である JavaScript で例外をキャッチしてエラー処理を実装する。

### エラーの状態処理

```ts
// useEventHandler.ts

import { useState } from 'react';

export function useErrorHandler(
  givenError?: unknown,
): (error: unknown) => void {
  const [error, setError] = useState<unknown>(null);
  if (givenError != null) throw givenError;
  if (error != null) throw error;
  return setError;
}
```

### エラーメッセージ用のユーティリティー

```ts
// useError.ts

import axios from 'axios';
import { useErrorHandler } from '../hooks/useErrorHandler';

/**
 * 共通エラー処理
 * @returns
 */
export const useError = () => {
  const handler = useErrorHandler();
  const showErrorMessage = (e: any) => {
    if (axios.isAxiosError(e) && e.response && e.response.status === 500) {
      alert(e.response.data.message);
      return;
    }
    handler(e);
  };

  return { showErrorMessage };
};

```
