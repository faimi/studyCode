我用了 antd5 的穿梭框，但是我新增了一个地区筛选功能，初始化的时候，我展示全国的医院，并把可选的数据添加到右边，但是当我变为浙江省的医院，即改变了 datasource 的值，右边的数据会变少，即不是浙江省的医院就消失了，我现在的需求是需要我切换地区的时候，被选中的数据一直都存在，用 react 写的

```tsx
import React, { useState, useEffect } from "react";
import { Transfer, Select } from "antd";

const allHospitals = {
  全国: [
    { key: "1", title: "北京医院" },
    { key: "2", title: "上海医院" },
    { key: "3", title: "浙江医院" },
    { key: "4", title: "广东医院" },
    { key: "5", title: "杭州医院" },
    { key: "6", title: "复旦附属医院" },
  ],
  浙江: [
    { key: "3", title: "浙江医院" },
  ],
  上海: [
    { key: "2", title: "上海医院" },
  ],
};

const TransferComponent = () => {
  const [province, setProvince] = useState("全国");
  const [dataSource, setDataSource] = useState(allHospitals[province] || []);
  const [targetKeys, setTargetKeys] = useState([]);

  // 监听筛选项 province 变化，确保 targetKeys 选中的数据不会丢失
  useEffect(() => {
    const newDataSource = allHospitals[province] || [];

    // 确保 targetKeys 中的医院不会因筛选而消失
    const selectedItems = targetKeys
      .map((key) => allHospitals["全国"].find((item) => item.key === key))
      .filter(Boolean);

    setDataSource([...new Set([...newDataSource, ...selectedItems])]);
  }, [province, targetKeys]);

  // 处理数据移动
  const handleChange = (newTargetKeys) => {
    setTargetKeys(newTargetKeys);
  };

  // 自定义左侧框头部，添加下拉筛选
  const renderHeader = () => (
    <div
      style={{
        display: "flex",
        justifyContent: "space-between",
        alignItems: "center",
      }}
    >
      <span>可选医院</span>
      <Select
        value={province}
        onChange={setProvince}
        size="small"
        style={{ width: 120 }}
      >
        <Select.Option value="全国">全国</Select.Option>
        <Select.Option value="浙江">浙江</Select.Option>
        <Select.Option value="上海">上海</Select.Option>
      </Select>
    </div>
  );

  return (
    <Transfer
      dataSource={dataSource}
      showSearch
      titles={[renderHeader(), "已选医院"]}
      targetKeys={targetKeys}
      onChange={handleChange}
      render={(item) => item.title}
      rowKey={(item) => item.key}
      filterOption={(inputValue, item) => item.title.includes(inputValue)}
      listStyle={{
        width: 250,
        height: 400,
      }}
    />
  );
};

export default TransferComponent;
```


