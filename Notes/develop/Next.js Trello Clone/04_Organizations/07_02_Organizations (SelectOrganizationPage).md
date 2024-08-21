## ファイル構成

```ts
├── app
    │   ├── (platform)
    │   │   ├── (clerk)
    │   │   │   ├── select-org
    │   │   │   │   └── [[...select-org]]
    │   │   │   │       └── page.tsx
```

## SelectOrganizationPage の作成

### app/(platform)/(clerk)/select-org/\[\[select-org]]/page.tsx

```tsx
import { OrganizationList } from '@clerk/nextjs';

export default function SelectOrganizationPage() {
  return <OrganizationList />;
}
```

## SelectOrganizationPage にアクセス

http://localhost:3000/select-org にアクセス

![[Pasted image 20240728142340.png]]

Create organization を選択する

## Create Organization 

![[Pasted image 20240728142459.png]]

Create organization ボタンを押下する

## Invite new members

参加メンバーを追加

![[Pasted image 20240728142604.png]]

Skip を選択 

## Choose an account

再び /select-org ページに遷移すると作成した Organization が選択可能になる

![[Pasted image 20240728142729.png]]

ただし選択しても何も起こらない

## Organization List の動作を変更する

- Personal account を隠す
- Organization 遷移後の遷移先を登録
- Organization 作成後の遷移先を登録

### app/(platform)/(clerk)/select-org/\[\[select-org]]/page.tsx

```tsx
import { OrganizationList } from '@clerk/nextjs';

export default function SelectOrganizationPage() {
  return (
    <OrganizationList
      hidePersonal
      afterSelectOrganizationUrl='/organization/:id'
      afterCreateOrganizationUrl='/organization/:id'
    />
  );
}
```
