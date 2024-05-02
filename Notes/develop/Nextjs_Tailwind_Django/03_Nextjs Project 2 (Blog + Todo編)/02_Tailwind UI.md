## テンプレート

有料だが500種類を超えるテンプレートが永久ラインセンスで手に入る

https://tailwindui.com/

## サインインフォーム

サインインページのサンプル

https://tailwindui.com/components/application-ui/forms/sign-in-forms

## 必要なライブラリを追加

```bash
npm i @tailwindcss/forms
```

## src/app/components/Auth.tsx

```tsx
export default function Auth() {
  return (
    <div className='max-w-md w-full space-y-8'>
      <div>
        <img
          className='mx-auto h-12 w-auto'
          src='https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg'
          alt='Workflow'
        />
        <h2 className='mt-6 text-center text-3xl font-extrabold text-white'>
          Sign in to your account
        </h2>
      </div>
      <form className='mt-8 space-y-6'>
        <input type='hidden' name='remember' value='true' />
        <div className='rounded-md shadow-sm -space-y-px'>
          <div>
            <input
              name='username'
              type='text'
              autoComplete='username'
              required
              className='appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm'
              placeholder='Username'
            />
          </div>
          <div>
            <input
              name='password'
              type='password'
              autoComplete='current-password'
              required
              className='appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm'
              placeholder='Password'
            />
          </div>
        </div>

        <div className='flex items-center justify-center'>
          <div className='text-sm'>
            <span className='cursor-pointer font-medium text-white hover:text-indigo-500'>
              change mode ?
            </span>
          </div>
        </div>

        <div>
          <button
            type='submit'
            className='group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500'
          >
            Sign in
          </button>
        </div>
      </form>
    </div>
  );
}
```