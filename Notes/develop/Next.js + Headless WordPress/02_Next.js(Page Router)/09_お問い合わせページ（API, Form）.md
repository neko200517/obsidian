## 概要

- 問い合わせページの作成 
- サーバサイドでフォーム処理を実行する

## フォルダ構成

```ts
.
├── pages
│   ├── api
│   │   └── form.ts
│   └── contact.tsx
```

## form APIを作成する

### pages/api/form.ts

```ts
import type { NextApiRequest, NextApiResponse } from 'next';

type RequestData = {
  firstName: string;
  email: string;
  message: string;
};

type ResponseData = {
  data: string;
};

export default function handler(
  req: NextApiRequest,
  res: NextApiResponse<ResponseData>
) {
  const body = req.body as RequestData;

  if (!body.firstName || !body.email || !body.message) {
    return res
      .status(400)
      .json({ data: 'first name, email, and message fields are required!' });
  }

  return res.status(200).json({ data: 'form submitted successfully' });
}
```

## お問い合わせページ全体

### pages/contact.tsx

```tsx
import { FormEvent, useState } from 'react';
import Head from 'next/head';
import { SiteHeader } from '@/components/SiteHeader';

export default function Contact() {
  const [submitState, setSubmitState] = useState(false);
  const [responseMessage, setResponseMessage] = useState('');
  const [alertColor, setAlertColor] = useState('bg-green-500');

  const handleSubmit = async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const formData = new FormData(e.currentTarget);

    const data = {
      firstName: formData.get('firstName'),
      email: formData.get('email'),
      message: formData.get('message'),
    };

    const response = await fetch('/api/form', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    });

    const result = await response.json();

    setSubmitState(true);
    setResponseMessage(result.data);
    setAlertColor(response.ok ? 'bg-green-500' : 'bg-red-500');
  };

  return (
    <>
      <Head>
        <title>Contact Us</title>
      </Head>
      <section className='bg-slate-700'>
        <SiteHeader className='header-contact' />
      </section>
      <section>
        <div className='container mx-auto lg:max-w-4xl'>
          <h1 className='text-4xl text-center text-slate-700 py-8'>
            Contact Us
          </h1>

          <form className='contact-form' onSubmit={handleSubmit}>
            <label htmlFor='firstName'>First Name:</label>
            <input type='text' id='firstName' name='firstName' />

            <label htmlFor='email'>Email:</label>
            <input type='email' id='email' name='email' />

            <label htmlFor='message'>Message:</label>
            <textarea
              name='message'
              id='message'
              cols={30}
              rows={10}
            ></textarea>

            <button type='submit'>Submit</button>
          </form>
          {submitState && (
            <SubmissionAlert
              message={responseMessage}
              alertColor={alertColor}
            />
          )}
        </div>
      </section>
    </>
  );
}

const SubmissionAlert = ({
  message,
  alertColor,
}: {
  message: string;
  alertColor: string;
}) => {
  return (
    <div className={`${alertColor} my-4 py-2 px-4 text-slate-100 rounded-md`}>
      {message}
    </div>
  );
};
```

## フォーム処理

- formタグのonSubmitに関数を割り当てる

```tsx
<form onSubmit={handleSubmit}>
    <button type='submit'>Submit</button>
</form>
```

## handleSubmit関数

- preventDefaultで再読込を無効化
- FormDataでフォームデータを取得
- fetch関数で/api/formを呼ぶ
	- エンドポイントが/api/form.tsに対応する
- bodyにオブジェクト→Json形式に変換して渡す

```tsx
  const handleSubmit = async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const formData = new FormData(e.currentTarget);

    const data = {
      firstName: formData.get('firstName'),
      email: formData.get('email'),
      message: formData.get('message'),
    };

    const response = await fetch('/api/form', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    });

    const result = await response.json();

    //...
  };
```