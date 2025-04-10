```tsx
const [zpCheckedList, setZpCheckedList] = useState<string[]>([]);
const [selectableZpOptions, setSelectableZpOptions] = useState<string[]>([]);

// 监听 hospitalDimensionGetZpInsurers 变化，并初始化 zpCheckedList
useEffect(() => {
  const selectableOptions = hospitalDimensionGetZpInsurers
    .filter((option) => !option.isExist)
    .map((option) => option.id);

  setSelectableZpOptions(selectableOptions);
  setZpCheckedList(selectableOptions); // 这里设置默认全选
}, [hospitalDimensionGetZpInsurers]); // 依赖 hospitalDimensionGetZpInsurers

// 判断是否全选
const checkAllZp = zpCheckedList.length === selectableZpOptions.length;
const zpIndeterminate =
  zpCheckedList.length > 0 && zpCheckedList.length < selectableZpOptions.length;

// 处理单个 Checkbox 变化
const onZpChange = (checked: boolean, value: string) => {
  setZpCheckedList((prevList) =>
    checked ? [...prevList, value] : prevList.filter((v) => v !== value)
  );
};

// 处理全选
const onCheckAllZpChange: CheckboxProps["onChange"] = (e) => {
  setZpCheckedList(e.target.checked ? selectableZpOptions : []);
};

return (
  <>
    <Checkbox
      checked={checkAllZp}
      indeterminate={zpIndeterminate}
      onChange={onCheckAllZpChange}
    >
      全选
    </Checkbox>

    {hospitalDimensionGetZpInsurers.map((option) => (
      <div key={option.id} className={styles.checkboxGroupGap}>
        <Checkbox
          value={option.id}
          checked={zpCheckedList.includes(option.id) || option.isExist}
          disabled={option.isExist}
          onChange={(e) => onZpChange(e.target.checked, option.id)}
        >
          {option.insurerName}
        </Checkbox>
        <Tag>{option.pattern}</Tag>
      </div>
    ))}
  </>
);
```
