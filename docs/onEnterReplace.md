# 自定义功能替换回车键

使用 `Alt+Enter`、`Shift+Enter`和`Ctrl + Enter` 替换回车的换行事件，将回车替换成业务逻辑。

## 技术依赖

- Reactjs
- Antd Form

## 代码实现

```tsx
import React, { useCallback } = 'react';
import { Form, Input } = 'antd';

default default function(){
    const [form] = Form.useForm();

    const onKeyDown = useCallback((e: React.KeyboardEvent<HTMLTextAreaElement>)=>{
        const keys: Array<'altKey' | 'ctrlKey' | 'shiftKey'> = ['altKey', 'ctrlKey', 'shiftKey'];

        if (keys.some(key => e[key]) && e.key === 'Enter') {
            // 阻断默认事件
            e.preventDefault();
            // 如果是组合键就输入换行符
            const target = e.target as HTMLTextAreaElement;
            const { selectionStart: start, selectionEnd: end } = target;

            const text: string = form.getFieldValue('text') || '';
            const newText = `${text.slice(0, start)}\n${text.slice(start)}`;


            form.setFieldsValue({
                text: newText,
            });

            // 因为视图渲染需要时间，所以延迟设置光标位置
            setTimeout(() => {
                target.setSelectionRange(start + 1, end + 1);
            });
        } else if (e.key === 'Enter') {
            // 阻断默认事件
            e.preventDefault();
            // Do something
        }
    }, []);

    return (
        <Form form={form}>
            <Form.Item name="text">
                <Input.TextArea onKeyDown={onKeyDown}>
            </Form.Item>
        </Form>
    );
}
```
