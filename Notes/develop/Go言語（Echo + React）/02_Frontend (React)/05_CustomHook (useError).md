## カスタムフックの作成

```bash
mkdir hooks
touch hooks/useError.ts
```

### エラーハンドリング用のカスタムフック 

APIから受け取ったレスポンスからエラーメッセージを表示するカスタムフック

#### hooks/useError.ts 

```ts
// hooks/useError.ts 

import axios from 'axios';
import { useNavigate } from 'react-router-dom';
import { CsrfToken } from '../types';
import useStore from '../store';

// エラーハンドリング用のカスタムフック
export const useError = () => {
  const navigate = useNavigate();
  const resetEditedTask = useStore((state) => state.resetEditedTask);

  const getCsrfToken = async () => {
    const { data } = await axios.get<CsrfToken>(
      `${import.meta.env.VITE_APP_API_URL}/csrf`
    );
    axios.defaults.headers.common['X-CSRF-Token'] = data.csrf_token;
  };

  const switchErrorHandling = (msg: string) => {
    switch (msg) {
      case 'invalid csrf token':
        // 無効なCSRFトークン
        getCsrfToken();
        alert('CSRF token is invalid, please try again');
        break;
      case 'invalid or expired jwt':
        // 無効または期限切れのjwt
        alert('access token expired, please login');
        resetEditedTask();
        navigate('/');
        break;
      case 'missing or malformed jwt':
        // jwt が見つからないか不正な形式です
        alert('access token is not valid, please login');
        resetEditedTask();
        navigate('/');
        break;
      case 'duplicated key not allowed':
        // 重複したキーは許可されません
        alert('email already exist, please use another one');
        break;
      case 'crypto/bcrypt: hashedPassword is not the hash of the given password':
        // crypto/bcrypt: hashedPassword は指定されたパスワードのハッシュではありません
        alert('password is not correct');
        break;
      case 'record not found':
        // 記録が見当たりませんでした
        alert('email is not correct');
        break;
      default:
        alert(msg);
    }
  };

  return { switchErrorHandling };
};
```