antd5 的 Table 里面有 Switch 去展示状态，然后在每行的编辑里会弹出 Modal，在这个 Modal 里面可以编辑他的状态，要求 Modal 里改完后，Table 里实时更新

```tsx
import React, { useState } from "react";
import { Table, Switch, Modal, Button } from "antd";

const App = () => {
  // 表格数据
  const [data, setData] = useState([
    { key: "1", name: "John Doe", status: true },
    { key: "2", name: "Jane Smith", status: false },
  ]);

  // 控制 Modal 的显示
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [currentRecord, setCurrentRecord] = useState(null);

  // 处理表格内的 Switch 变化
  const handleTableSwitchChange = (checked, record) => {
    setData((prevData) =>
      prevData.map((item) =>
        item.key === record.key ? { ...item, status: checked } : item
      )
    );
  };

  // 处理 Modal 内部的 Switch 变化
  const handleModalSwitchChange = (checked) => {
    setCurrentRecord((prevRecord) => ({ ...prevRecord, status: checked }));
  };

  // 关闭 Modal 并更新表格数据
  const handleModalOk = () => {
    setData((prevData) =>
      prevData.map((item) =>
        item.key === currentRecord.key ? currentRecord : item
      )
    );
    setIsModalOpen(false);
  };

  // 打开 Modal
  const showModal = (record) => {
    setCurrentRecord({ ...record }); // 复制当前行的数据
    setIsModalOpen(true);
  };

  // 表格列
  const columns = [
    {
      title: "Name",
      dataIndex: "name",
      key: "name",
    },
    {
      title: "Status",
      dataIndex: "status",
      key: "status",
      render: (text, record) => (
        <Switch
          checked={record.status}
          onChange={(checked) => handleTableSwitchChange(checked, record)}
        />
      ),
    },
    {
      title: "Action",
      key: "action",
      render: (_, record) => (
        <Button type="link" onClick={() => showModal(record)}>
          Edit
        </Button>
      ),
    },
  ];

  return (
    <div>
      <Table columns={columns} dataSource={data} />

      {/* 编辑 Modal */}
      <Modal
        title="Edit Status"
        open={isModalOpen}
        onOk={handleModalOk}
        onCancel={() => setIsModalOpen(false)}
      >
        {currentRecord && (
          <Switch
            checked={currentRecord.status}
            onChange={handleModalSwitchChange}
          />
        )}
      </Modal>
    </div>
  );
};

export default App;
```
