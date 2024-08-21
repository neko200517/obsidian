## ShadUI - Textarea の導入

```bash
npx shadcn-ui@latest add textarea
```

## FormTextarea を作成する

- アプリで使用するフォーム用のコンポーネントの作成

### components/form/form-textarea.tsx

```tsx
'use client';

import { KeyboardEventHandler, forwardRef } from 'react';
import { useFormStatus } from 'react-dom';

import { Label } from '@/components/ui/label';
import { Textarea } from '@/components/ui/textarea';
import { cn } from '@/lib/utils';

import FormErrors from '@/components/form/form-errors';

interface FormTextareaProps {
  id: string;
  label?: string;
  placeholder?: string;
  required?: boolean;
  disabled?: boolean;
  errors?: Record<string, string[] | undefined>;
  className?: string;
  onBlur?: () => void;
  onClick?: () => void;
  onKeyDown?: KeyboardEventHandler<HTMLTextAreaElement> | undefined;
  defaultValue?: string;
}

export const FormTextarea = forwardRef<HTMLTextAreaElement, FormTextareaProps>(
  function FormTextarea(
    {
      id,
      label,
      placeholder,
      required,
      disabled,
      errors,
      className,
      onBlur,
      onClick,
      onKeyDown,
      defaultValue,
    },
    ref
  ) {
    const { pending } = useFormStatus();

    return (
      <div className='space-y-2 w-full'>
        <div className='space-y-1 w-full'>
          {label ? (
            <Label
              htmlFor={id}
              className='text-xs font-semibold text-neutral-700'
            >
              {label}
            </Label>
          ) : null}
        </div>

        <Textarea
          ref={ref}
          id={id}
          name={id}
          placeholder={placeholder}
          required={required}
          disabled={disabled || pending}
          className={cn(
            'resize-none focus-visible:ring-0 focus-visible:ring-offset-0 ring-0 focus:ring-0 outline-none shadow-sm',
            className
          )}
          onBlur={onBlur}
          onClick={onClick}
          onKeyDown={onKeyDown}
          aria-describedby={`${id}-error`}
          defaultValue={defaultValue}
        />
        <FormErrors id={id} errors={errors} />
      </div>
    );
  }
);

FormTextarea.displayName = 'FormTextarea';

export default FormTextarea;
```