---
group: 
  title: 数据结构化
  order: 2
---

# ProFormList 数据结构化

我们还提供了用来进行结构化数据的录入:

引入方法  `import { ProFormList } from '@ant-design/pro-components';`

- ProFormList 录入结构化的多维数组数据。
- ProFormFieldSet 录入结构化的一维数组数据。
- ProFormDependency 数据依赖的相关组件

## ProFormList

ProFormList 与 [Form.List](https://ant.design/components/form-cn/#Form.List) API 基本相同，增加了自带的操作按钮：删除和复制，并且自带了了一个`新建一行`按钮。

```tsx | pure
<ProFormList
  name="users"
  initialValue={[
    {
      useMode: 'chapter',
    },
  ]}
  creatorButtonProps={{
    position: 'top',
    creatorButtonText: '再建一行',
  }}
  creatorRecord={{
    useMode: 'none',
  }}
>
  <ProFormSelect
    key="useMode"
    options={[
      {
        value: 'chapter',
        label: '盖章后生效',
      },
      {
        value: 'none',
        label: '不生效',
      },
    ]}
    width="md"
    name="useMode"
    label="合同约定生效方式"
  />
</ProFormList>
```

### actionRef 操作项目实例

```tsx | pure
const actionRef = useRef<
  FormListActionType<{
    name: string;
  }>
>();

return (
  /**
   * @name 获取到 list 操作实例
   * @description 可用删除，新增，移动等操作
   *
   * @example  actionRef?.current.add?.({},1);
   * @example  actionRef?.current.remove?.(1);
   * @example  actionRef?.current.move?.(1,2);
   * @example  actionRef?.current.get?.(1);
   */
  <>
    <Space>
      <Button
        type="primary"
        onClick={() => {
          const list = actionRef.current?.getList();
          actionRef.current?.add({
            name: '新增' + list?.length,
          });
        }}
      >
        增加一行
      </Button>
      <Button
        danger
        onClick={() => {
          actionRef.current?.remove(1);
        }}
      >
        删除一行
      </Button>
      <Button
        onClick={() => {
          actionRef.current?.move(1, 0);
        }}
      >
        移动到第一行
      </Button>
      <Button
        type="dashed"
        onClick={() => {
          const row = actionRef.current?.get(1);
          console.log(row);
        }}
      >
        获取一行数据
      </Button>
      <Button
        type="dashed"
        onClick={() => {
          const row = actionRef.current?.getList();
          console.log(row);
        }}
      >
        获取所有数据
      </Button>
    </Space>
    <ProFormList actionGuard={actionGuard} actionRef={actionRef}>
      <ProFormText key="useMode" name="name" label="姓名" />
    </ProFormList>
  </>
);
```

### actionGuard 拦截器

actionGuard 可以拦截 list 的操作，现在有 `beforeAddRow` 和 `beforeRemoveRow` 两个事件。

```tsx | pure
const actionRef = useRef<
  FormListActionType<{
    name: string;
  }>
>();
const actionGuard = {
  beforeAddRow: async (defaultValue, insertIndex, count) => {
    return new Promise((resolve) => {
      console.log(defaultValue?.name, insertIndex, count);
      setTimeout(() => resolve(true), 1000);
    });
  },
  beforeRemoveRow: async (index, count) => {
    const row = actionRef.current?.get(index as number);
    console.log('--->', index, count, row);
    return new Promise((resolve) => {
      if (index === 0) {
        resolve(false);
        return;
      }
      setTimeout(() => resolve(true), 1000);
    });
  },
};
return (
  <ProFormList actionGuard={actionGuard} actionRef={actionRef}>
    <ProFormText key="useMode" name="name" label="姓名" />
  </ProFormList>
);
```

### actionRender 自定义操作按钮

```tsx | pure
  /**
   * @name 自定义操作按钮
   *
   * @example 删除按钮
   * actionRender:(field,action)=><a onClick={()=>action.remove(field.name)}>删除</a>
   * @example 最多只能新增三行
   * actionRender:(f,action,_,count)=><a onClick={()=>
   *   count>2?alert("最多三行！"):action.add({id:"xx"})}>删除
   * </a>
   */
  actionRender?: (
    field: FormListFieldData,
    /**
     * 操作能力
     * @example  action.add(data) 新增一行
     * @example  action.remove(index) 删除一行
     * @example  action.move(formIndex,targetIndex) 移动一行
     */
    action: FormListOperation,
    /**
     * 默认的操作dom
     * [复制，删除]
     */
    defaultActionDom: ReactNode[],
    /**
     * 当前共有几个列表项
     */
    count: number,
  ) => ReactNode[];
```

### ProFormList RenderProps 模式

ProFormList 支持传入一个方法来获取到当前行的信息和快捷操作，这对于复杂的联动来说是很方便的。

```tsx | pure
<ProFormList>
  {(
    // 当前行的基本信息 {name: number; key: number}
    meta,
    // 当前的行号
    index,
    /**
     * action
     * @name 用于操作行的一些快捷方法
     * @example 给第二行增加数据 action.add?.({},1);
     * @example 删除第二行 action.remove?.(1);
     * @example 从 1 移到 2: action.move?.(2,1);
     * @example 获取当前行的数据: action.getCurrentRowData() -> {id:"xxx",name:'123',age:18}
     * @example 设置当前行的数据: {id:"123",name:'123'} -> action.setCurrentRowData({name:'xxx'}) -> {id:"123",name:'xxx'}
     * @example 清空当前行的数据：{id:"123",name:'123'} -> action.setCurrentRowData({name:undefined}) -> {id:"123"}
     */
    action,
    // 总行数
    count,
  ) => {
    return (
      <div key="row">
        <ProFormText name="id" />
        <ProFormText name="name" />
      </div>
    );
  }}
</ProFormList>
```

这四个参数的类型定义如下：

```tsx | pure
type RenderActionParams = {
  /**
   * @name 当前行的meta信息
   * @example {name: number; key: number}
   */
  meta: FormListFieldData;
  /**
   * @name 当前行的行号
   */
  index: number;
  /**
   * @name 用于操作行的一些快捷方法
   * @example 给第二行增加数据 action.add?.({},1);
   * @example 删除第二行 action.remove?.(1);
   * @example 从 1 移到 2: action.move?.(2,1);
   * @example 获取当前行的数据: action.getCurrentRowData() -> {id:"xxx",name:'123',age:18}
   * @example 设置当前行的数据: {id:"123",name:'123'} -> action.setCurrentRowData({name:'xxx'}) -> {id:"123",name:'xxx'}
   * @example 清空当前行的数据：{id:"123",name:'123'} -> action.setCurrentRowData({name:undefined}) -> {id:"123"}
   */
  action: FormListOperation & {
    /**
     * @name 获取当前行的数据
     * @example getCurrentRowData -> {id:"xxx",name:'123',age:18}
     */
    getCurrentRowData: () => any;
    /**
     * @name 设置当前行的数据
     * @example {id:"123",name:'123'} -> setCurrentRowData({name:'xxx'}) -> {id:"123",name:'123'}
     * @example {id:"123",name:'123'} -> setCurrentRowData({name:undefined}) -> {id:"123"}
     */
    setCurrentRowData: (data: any) => void;
  };
  /**
   * @name 总行数
   */
  count: number;
};
```

## ProFormFieldSet

ProFormFieldSet 可以将内部的多个 children 的值组合并且存储在 ProForm 中，并且可以通过 `transform` 在提交时转化。下面是一个简单的用法，可以方便的组合多个输入框，并且格式化为想要的数据。

```tsx | pure
<ProFormFieldSet
  name="list"
  label="组件列表"
  // 支持 两种方式，type="group" 会用input.group 包裹
  // 如果不配置 默认使用 space
  type="group"
  transform={(value: any) => ({ startTime: value[0], endTime: value[1] })}
>
  <ProFormText width="md" />
  <ProFormText width="md" />
  <ProFormText width="md" />
</ProFormFieldSet>
```

## ProFormDependency

ProFormDependency 是一个简化版本的 Form.Item，它默认内置了 noStyle 与 shouldUpdate，我们只需要配置 name 来确定我们依赖哪个数据，ProFormDependency 会自动处理 diff 和并且从表单中提取相应的值。

name 参数必须要是一个数组，如果是嵌套的结构可以这样配置 `name={['name', ['name2', 'text']]}`。配置的 name 的值会在 renderProps 中传入。`name={['name', ['name2', 'text']]}` 传入的 values 的值 为 `{ name: string,name2: { text:string } }`。

```tsx | pure
<ProFormDependency name={['name']}>
  {({ name }) => {
    return (
      <ProFormSelect
        options={[
          {
            value: 'chapter',
            label: '盖章后生效',
          },
        ]}
        width="md"
        name="useMode"
        label={`与《${name}》合同约定生效方式`}
      />
    );
  }}
</ProFormDependency>
```

## 代码示例

### 自定义删除和复制的 tooltip

```tsx
import { CloseCircleOutlined, SmileOutlined } from '@ant-design/icons';
import {
  ProForm,
  ProFormGroup,
  ProFormList,
  ProFormText,
} from '@ant-design/pro-components';
import { Segmented } from 'antd';
import { useState } from 'react';

const Demo = () => {
  const [readonly, setReadonly] = useState(false);
  return (
    <div
      style={{
        display: 'flex',
        flexDirection: 'column',
        gap: 16,
      }}
    >
      <ProForm.Item name="mode" label="模式">
        <Segmented
          block
          options={[
            {
              label: '编辑',
              title: '编辑',
              value: 'edit',
            },
            {
              label: '只读',
              title: '只读',
              value: 'readonly',
            },
          ]}
          onChange={(e) => {
            setReadonly(e === 'readonly');
          }}
        />
      </ProForm.Item>
      <ProForm readonly={readonly} onFinish={async (e) => console.log(e)}>
        <ProFormText name="name" label="姓名" />

        <ProFormList
          name="labels"
          label="用户信息"
          initialValue={[
            {
              value: '333',
              label: '333',
            },
          ]}
          copyIconProps={{ Icon: SmileOutlined, tooltipText: '复制此项到末尾' }}
          deleteIconProps={{
            Icon: CloseCircleOutlined,
            tooltipText: '不需要这行了',
          }}
        >
          <ProFormGroup key="group">
            <ProFormText name="value" label="值" />
            <ProFormText name="label" label="显示名称" />
          </ProFormGroup>
        </ProFormList>
      </ProForm>
    </div>
  );
};

export default Demo;
```

### 联动的 FormList

```tsx
import {
  ProForm,
  ProFormDependency,
  ProFormList,
  ProFormText,
} from '@ant-design/pro-components';
import { Button } from 'antd';

const Demo = () => {
  return (
    <ProForm>
      <ProFormList
        name={['default', 'users']}
        label="用户信息"
        initialValue={[
          {
            name: '1111',
          },
        ]}
        itemContainerRender={(doms) => {
          return <ProForm.Group>{doms}</ProForm.Group>;
        }}
        alwaysShowItemLabel
      >
        {(f, index, action) => {
          console.log(f, index, action);
          return (
            <>
              <ProFormText
                initialValue={index}
                name="rowKey"
                label={`第 ${index} 配置`}
              />
              <ProFormText name="name" key="name" label="姓名" />
              <ProFormDependency key="remark" name={['name']}>
                {({ name }) => {
                  if (!name) {
                    return (
                      <span
                        style={{
                          lineHeight: '92px',
                        }}
                      >
                        输入姓名展示
                      </span>
                    );
                  }
                  return <ProFormText name="remark" label="昵称详情" />;
                }}
              </ProFormDependency>
              <div
                style={{
                  display: 'flex',
                  alignItems: 'flex-end',
                  gap: '8px',
                  height: 60,
                }}
              >
                <Button
                  type="primary"
                  key="SET"
                  onClick={() => {
                    action.setCurrentRowData({
                      name: 'New Name' + index,
                      remark: 'New Remark' + index,
                    });
                  }}
                >
                  设置此项
                </Button>

                <Button
                  type="dashed"
                  key="clear"
                  onClick={() => {
                    action.setCurrentRowData({
                      name: undefined,
                      remark: undefined,
                    });
                  }}
                >
                  清空此项
                </Button>
              </div>
            </>
          );
        }}
      </ProFormList>
    </ProForm>
  );
};

export default Demo;
```

### 可调整的新建按钮位置

```tsx
import {
  ProForm,
  ProFormDatePicker,
  ProFormDigit,
  ProFormFieldSet,
  ProFormGroup,
  ProFormList,
  ProFormRadio,
  ProFormSelect,
  ProFormText,
} from '@ant-design/pro-components';
import { ConfigProvider } from 'antd';
import { useState } from 'react';

const Demo = () => {
  const [position, setPosition] = useState<'bottom' | 'top'>('bottom');
  return (
    <ConfigProvider componentSize="small">
      <ProFormRadio.Group
        fieldProps={{
          value: position,
          onChange: (e) => setPosition(e.target.value),
        }}
        options={[
          {
            label: '顶部',
            value: 'top',
          },
          {
            label: '底部',
            value: 'bottom',
          },
        ]}
      />
      <ProForm
        onFinish={async (values) => {
          console.log('Received values of form:', values);
        }}
      >
        <ProFormText width="sm" name="id" label="主合同编号" />
        <ProFormText
          name="project"
          width="md"
          label="项目名称"
          initialValue="xxxx项目"
        />
        <ProFormText
          width="xs"
          name="mangerName"
          label="商务经理"
          initialValue="启途"
        />
        <ProFormList
          name="users"
          label="用户信息"
          rules={[
            {
              required: true,
              validator: async (_, value) => {
                console.log(value);
                if (value && value.length > 0) {
                  return;
                }
                throw new Error('至少要有一项！');
              },
            },
          ]}
          creatorButtonProps={{
            position,
          }}
          creatorRecord={{
            name: 'test',
          }}
          initialValue={[
            {
              name: '1111',
              nickName: '1111',
              age: 111,
              birth: '2021-02-18',
              sex: 'man',
              addrList: [{ addr: ['taiyuan', 'changfeng'] }],
            },
          ]}
        >
          <ProFormGroup key="group">
            <ProFormText
              rules={[
                {
                  required: true,
                },
              ]}
              name="name"
              label="姓名"
            />
            <ProFormDigit name="age" label="年龄" width="sm" />
            <ProFormSelect
              label="性别"
              name="sex"
              width="xs"
              valueEnum={{
                man: '男性',
                woman: '女性',
              }}
            />
            <ProFormDatePicker name="birth" label="出生日期" />
            <ProFormFieldSet name="addr" label="地址">
              <ProFormSelect
                valueEnum={{
                  taiyuan: '山西',
                  hangzhou: '杭州',
                }}
              />
              <ProFormSelect
                valueEnum={{
                  changfeng: '长风街',
                  gongzhuan: '工专路',
                }}
              />
            </ProFormFieldSet>
          </ProFormGroup>
        </ProFormList>
      </ProForm>
    </ConfigProvider>
  );
};

export default Demo;
```

### 表单互相嵌套

```tsx
import {
  ProCard,
  ProForm,
  ProFormGroup,
  ProFormList,
  ProFormText,
} from '@ant-design/pro-components';

const Demo = () => {
  return (
    <ProForm onFinish={async (e) => console.log(e)}>
      <ProFormText name="name" label="姓名" />
      <ProFormList
        name="users"
        label="用户信息"
        initialValue={[
          {
            name: '1111',
          },
        ]}
        itemRender={({ listDom, action }, { record }) => {
          return (
            <ProCard
              bordered
              extra={action}
              title={record?.name}
              style={{
                marginBlockEnd: 8,
              }}
            >
              {listDom}
            </ProCard>
          );
        }}
      >
        <ProFormGroup>
          <ProFormText name="name" label="姓名" />
          <ProFormText name="nickName" label="昵称" />
        </ProFormGroup>
        <ProFormList
          name="labels"
          label="用户信息"
          initialValue={[
            {
              value: '333',
              label: '333',
            },
          ]}
          copyIconProps={{
            tooltipText: '复制此项到末尾',
          }}
          deleteIconProps={{
            tooltipText: '不需要这行了',
          }}
        >
          <ProFormGroup key="group">
            <ProFormText name="value" label="值" />
            <ProFormText name="label" label="显示名称" />
          </ProFormGroup>
        </ProFormList>
      </ProFormList>
    </ProForm>
  );
};

export default Demo;
```

### 复杂联动

```tsx
import {
  ProForm,
  ProFormDependency,
  ProFormList,
  ProFormSelect,
  ProFormText,
} from '@ant-design/pro-components';

const Demo = () => {
  return (
    <ProForm>
      <ProFormList
        name={['default', 'users']}
        label="用户信息"
        initialValue={[
          {
            name: '我是姓名',
          },
        ]}
        itemContainerRender={(doms) => {
          return <ProForm.Group>{doms}</ProForm.Group>;
        }}
      >
        {(f, index, action) => {
          console.log(f, index, action);
          return (
            <>
              <ProFormText
                initialValue={index}
                name="rowKey"
                label={`第 ${index} 配置`}
              />
              <ProFormText name="name" label="姓名" />
              <ProFormDependency name={['name']}>
                {({ name }) => {
                  if (!name) {
                    return (
                      <span
                        style={{
                          lineHeight: '32px',
                        }}
                      >
                        输入姓名展示
                      </span>
                    );
                  }
                  return <ProFormText name="remark" label="昵称详情" />;
                }}
              </ProFormDependency>
              <ProFormSelect
                name="addr"
                width="md"
                label="与 name 联动的选择器"
                dependencies={['name']}
                request={async (params) => [
                  { label: params.name, value: 'all' },
                  { label: 'Unresolved', value: 'open' },
                  { label: 'Resolved', value: 'closed' },
                  { label: 'Resolving', value: 'processing' },
                ]}
              />
            </>
          );
        }}
      </ProFormList>
    </ProForm>
  );
};

export default Demo;
```

### 行为守卫

```tsx
import type { FormListActionType } from '@ant-design/pro-components';
import {
  ProCard,
  ProForm,
  ProFormGroup,
  ProFormList,
  ProFormText,
} from '@ant-design/pro-components';
import { Button, Space, message } from 'antd';
import { useRef } from 'react';

const Demo = () => {
  const actionRef = useRef<
    FormListActionType<{
      name: string;
    }>
  >();
  return (
    <>
      <Space
        style={{
          marginBlockEnd: 24,
        }}
      >
        <Button
          type="primary"
          onClick={() => {
            const list = actionRef.current?.getList();
            actionRef.current?.add({
              name: '新增' + list?.length,
            });
          }}
        >
          增加一行
        </Button>
        <Button
          danger
          onClick={() => {
            actionRef.current?.remove(1);
          }}
        >
          删除一行
        </Button>
        <Button
          onClick={() => {
            actionRef.current?.move(1, 0);
          }}
        >
          移动到第一行
        </Button>
        <Button
          type="dashed"
          onClick={() => {
            const row = actionRef.current?.get(1);
            console.log(row);
          }}
        >
          获取一行数据
        </Button>
        <Button
          type="dashed"
          onClick={() => {
            const row = actionRef.current?.getList();
            console.log(row);
          }}
        >
          获取所有数据
        </Button>
      </Space>
      <ProForm onFinish={async (e) => console.log(e)}>
        <ProFormList
          name="users"
          label="用户信息"
          initialValue={[
            {
              name: '1111',
            },
          ]}
          creatorRecord={{
            name: '222',
          }}
          actionGuard={{
            beforeAddRow: async (defaultValue, insertIndex) => {
              return new Promise((resolve) => {
                console.log(defaultValue, insertIndex);
                setTimeout(() => resolve(true), 1000);
              });
            },
            beforeRemoveRow: async (index) => {
              return new Promise((resolve) => {
                if (index === 0) {
                  message.error('这行不能删');
                  resolve(false);
                  return;
                }
                setTimeout(() => resolve(true), 1000);
              });
            },
          }}
          itemRender={({ listDom, action }, { record }) => {
            return (
              <ProCard
                bordered
                extra={action}
                title={record?.name}
                style={{
                  marginBlockEnd: 8,
                }}
              >
                {listDom}
              </ProCard>
            );
          }}
          actionRef={actionRef}
        >
          <ProFormGroup key="group">
            <ProFormText name="name" label="姓名" />
            <ProFormText name="age" label="年龄" />
          </ProFormGroup>
        </ProFormList>
      </ProForm>
    </>
  );
};

export default Demo;
```

### 增删条目限制

```tsx
import { CloseOutlined, SnippetsOutlined } from '@ant-design/icons';
import type { FormListActionType } from '@ant-design/pro-components';
import { ProForm, ProFormList, ProFormText } from '@ant-design/pro-components';
import { useRef } from 'react';

export default () => {
  const actionRef = useRef<
    FormListActionType<{
      name: string;
    }>
  >();
  return (
    <>
      <ProForm>
        <ProFormList
          copyIconProps={{
            Icon: SnippetsOutlined,
          }}
          deleteIconProps={{
            Icon: CloseOutlined,
          }}
          min={1}
          max={4}
          actionRef={actionRef}
          actionGuard={{
            beforeAddRow: async (defaultValue, insertIndex, count) => {
              return new Promise((resolve) => {
                console.log(defaultValue?.name, insertIndex, count);
                setTimeout(() => resolve(true), 1000);
              });
            },
            beforeRemoveRow: async (index, count) => {
              const row = actionRef.current?.get(index as number);
              console.log('--->', index, count, row);
              return new Promise((resolve) => {
                if (index === 0) {
                  resolve(false);
                  return;
                }
                setTimeout(() => resolve(true), 1000);
              });
            },
          }}
          name="users"
          label="用户信息"
          initialValue={[
            {
              name: '1111',
            },
          ]}
        >
          <ProFormText key="useMode" name="name" label="姓名" />
        </ProFormList>
      </ProForm>
    </>
  );
};
```

### 横向布局

```tsx
import {
  ProCard,
  ProForm,
  ProFormList,
  ProFormText,
} from '@ant-design/pro-components';

const Demo = () => {
  return (
    <ProForm layout="horizontal">
      <ProFormList
        name="attributes"
        label="规格"
        creatorButtonProps={{
          creatorButtonText: '添加规格项',
        }}
        min={1}
        copyIconProps={false}
        itemRender={({ listDom, action }, { index }) => (
          <ProCard
            bordered
            style={{ marginBlockEnd: 8 }}
            title={`规格${index + 1}`}
            extra={action}
            bodyStyle={{ paddingBlockEnd: 0 }}
          >
            {listDom}
          </ProCard>
        )}
        creatorRecord={{ name: '', items: [{ name: '' }] }}
        initialValue={[
          { name: '颜色', items: [{ name: '红' }, { name: '黄' }] },
        ]}
      >
        <ProFormText
          style={{ padding: 0 }}
          width="md"
          name="name"
          label="规格名"
        />
        <ProForm.Item isListField style={{ marginBlockEnd: 0 }} label="规格值">
          <ProFormList
            name="items"
            creatorButtonProps={{
              creatorButtonText: '新建',
              icon: false,
              type: 'link',
              style: { width: 'unset' },
            }}
            min={1}
            copyIconProps={false}
            deleteIconProps={{ tooltipText: '删除' }}
            itemRender={({ listDom, action }) => (
              <div
                style={{
                  display: 'inline-flex',
                  marginInlineEnd: 25,
                }}
              >
                {listDom}
                {action}
              </div>
            )}
          >
            <ProFormText allowClear={false} width="xs" name={['name']} />
          </ProFormList>
        </ProForm.Item>
      </ProFormList>
    </ProForm>
  );
};

export default Demo;
```