## src/app/contact/page.tsx

・ユーザープロフィールの静的ページを表示する
・カードの配置
・アバターの配置 
・プロフィールの配置 
・SNSアイコンの配置（※事前にアイコンをpublicディレクトリに配置しておくこと）

```tsx
import { Metadata } from 'next';
import Image from 'next/image';

export const metadata: Metadata = {
  title: 'Contact',
};

const Contact = () => {
  return (
    <div className='bg-white text-center shadow-xl p-8 w-80 rounded'>
      <div className='mt-4'>
        <p className='font-bold'>Contact info</p>
        <div className='flex justify-center mt-4'>
          <Image
            className='rounded-full'
            src='/avatar.jpg'
            width={60}
            height={60}
            alt='Avatar'
          ></Image>
        </div>
        <div className='mt-4'>
          <p className='font-bold'>Address</p>
          <p className='text-xs mt-2 text-gray-600'>city A</p>
          <p className='font-bold mt-3'>E-mail</p>
          <p className='text-xs mt-2 text-gray-600'>abc@gmail.com</p>
          <p className='font-bold mt-3'>Phone</p>
          <p className='text-xs mt-2 text-gray-600'>000-123-456</p>
        </div>
        <div className='mt-6 flex justify-around'>
          <a
            className='mr-3'
            href='https://twitter.com/'
            target='_blank'
            rel='noopener noreferrer'
          >
            <Image src='/x-logo.svg' width={24} height={24} alt='X-logo' />
          </a>
          <a
            className='mr-3'
            href='https://www.facebook.com/'
            target='_blank'
            rel='noopener noreferrer'
          >
            <Image src='/facebook.svg' width={24} height={24} alt='Github' />
          </a>
          <a
            className='mr-3'
            href='https://github.com/'
            target='_blank'
            rel='noopener noreferrer'
          >
            <Image src='/github-mark.svg' width={24} height={24} alt='Github' />
          </a>
        </div>
      </div>
    </div>
  );
};

export default Contact;
```