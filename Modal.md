可以用这种写法控制 Modal 组件的开关

```tsx
// 父组件
const feedbackModalActionsRef = useRef<Actions>();

<FeedbackModal feedbackModalActionsRef={feedbackModalActionsRef} />;

// 显示FeedbackModal组件
feedbackModalActionsRef.current?.setTrue();

// 隐藏FeedbackModal组件
feedbackModalActionsRef.current?.setFalse();
```

```tsx
// 子组件 FeedbackModal
const [visible, action] = useBoolean();

// 将action对象赋值给feedbackModalActionsRef.current
feedbackModalActionsRef.current = action;
```
