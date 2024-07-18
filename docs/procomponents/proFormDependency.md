---
group: 
  title: 数据联动
  order: 3
---

# ProFormDependency 数据联动

Form 中的数据联动非常常见，所以我们封装了一个组件来进行数据处理。

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

### 互相依赖表单

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
      <ProFormSelect
        options={[
          {
            value: 'select',
            label: '选择框',
          },
          {
            value: 'input',
            label: '输入框',
          },
        ]}
        width="xs"
        name="globalUseMode"
        label="全局生效方式组件的类型"
      />
      <ProFormList
        name={['default', 'users']}
        label="用户信息"
        initialValue={[
          {
            name: '1111',
          },
        ]}
        alwaysShowItemLabel
      >
        <ProForm.Group key="group">
          <ProFormSelect
            options={[
              {
                value: 'select',
                label: '选择框',
              },
              {
                value: 'input',
                label: '输入框',
              },
            ]}
            width="xs"
            name="useMode"
            label="生效方式组件的类型"
          />
          <ProFormDependency name={['useMode']}>
            {({ useMode }) => {
              if (useMode === 'select') {
                return (
                  <ProFormSelect
                    options={[
                      {
                        value: 'chapter',
                        label: '盖章后生效',
                      },
                    ]}
                    width="md"
                    name="function"
                    label="生效方式"
                  />
                );
              }
              return (
                <ProFormText width="md" name="function" label="生效方式" />
              );
            }}
          </ProFormDependency>

          <ProFormDependency
            key="globalUseMode"
            name={['globalUseMode']}
            ignoreFormListField
          >
            {({ globalUseMode }) => {
              if (globalUseMode === 'select') {
                return (
                  <ProFormSelect
                    options={[
                      {
                        value: 'chapter',
                        label: '盖章后生效',
                      },
                    ]}
                    width="md"
                    name="gfunction"
                    label="外层联动生效方式"
                  />
                );
              }
              return (
                <ProFormText
                  width="md"
                  name="gfunction"
                  label="外层联动生效方式"
                />
              );
            }}
          </ProFormDependency>
        </ProForm.Group>
      </ProFormList>
    </ProForm>
  );
};

export default Demo;
```

### 获取表单依赖值

下面例子演示了不同情形下的依赖取值顺序：

- `<ProFormDependency>`**不在**`<ProFormList>`中：根据`name`声明的依赖项，从全局取值（情形 1）
- `<ProFormDependency>`**在**`<ProFormList>`中
  - `<ProFormDependency>`的`ignoreFormListField`为`true`：根据`name`声明的依赖项，从全局取值（情形 2）
  - `<ProFormDependency>`的`ignoreFormListField`为`false`：根据`name`声明的依赖项，从局部取值（情形 3）

```tsx
import {
  ProForm,
  ProFormDependency,
  ProFormGroup,
  ProFormList,
  ProFormText,
} from '@ant-design/pro-components';
import { Form } from 'antd';
import type { NamePath } from 'antd/lib/form/interface';

const Demo = () => {
  const initialValues = {
    a: 1,
    b: 2,
    c: {
      a: 3,
      b: 4,
      c: {
        a: 5,
      },
      d: [{ a: 6, b: 7 }],
      e: [{ a: 8, b: 9 }],
    },
  };
  const depName1: NamePath[] = [
    'a',
    'b',
    ['c', 'a'],
    ['c', 'b'],
    ['c', 'c', 'a'],
    ['c', 'd'],
    ['c', 'e'],
  ];
  const depName2: NamePath[] = ['a', 'b', ['c', 'a']];
  const depName3: NamePath[] = ['a', 'b', ['c', 'a']];
  return (
    <ProForm initialValues={initialValues}>
      <ProFormGroup>
        <ProFormText name="a" label="a" />
        <ProFormText name="b" label="b" />
        <ProFormText name={['c', 'a']} label="c.a" />
        <ProFormText name={['c', 'b']} label="c.b" />
        <ProFormText name={['c', 'c', 'a']} label="c.c.a" />
        <ProFormGroup title="c.d">
          <ProFormList name={['c', 'd']}>
            <ProFormGroup key="group">
              <ProFormText name="a" label="a" />
              <ProFormText name="b" label="b" />
              <ProFormDependency name={depName3}>
                {(depValues) => (
                  <Form.Item
                    label={`搜集依赖值（情形3） <ProFormDependency name={${JSON.stringify(
                      depName3,
                    )}}>`}
                    extra="a, b, c.a取自局部"
                  >
                    <pre>
                      <code>{JSON.stringify(depValues, null, 2)}</code>
                    </pre>
                  </Form.Item>
                )}
              </ProFormDependency>
            </ProFormGroup>
          </ProFormList>
        </ProFormGroup>
        <ProFormGroup title="c.e">
          <ProFormList name={['c', 'e']}>
            <ProFormGroup key="group">
              <ProFormText name="a" label="a" />
              <ProFormText name="b" label="b" />
              <ProFormDependency name={depName2} ignoreFormListField>
                {(depValues) => (
                  <Form.Item
                    label={`搜集依赖值（情形2) <ProFormDependency name={${JSON.stringify(
                      depName2,
                    )}} ignoreFormListField>`}
                    extra="a, b, c.a取自全局"
                  >
                    <pre>
                      <code>{JSON.stringify(depValues)}</code>
                    </pre>
                  </Form.Item>
                )}
              </ProFormDependency>
            </ProFormGroup>
          </ProFormList>
        </ProFormGroup>
      </ProFormGroup>
      <ProFormGroup
        title={`收集依赖值（情形1) <ProFormDependency name={${JSON.stringify(
          depName1,
        )}}>`}
      >
        <ProFormDependency name={depName1}>
          {(depValues) => (
            <pre>
              <code>{JSON.stringify(depValues)}</code>
            </pre>
          )}
        </ProFormDependency>
      </ProFormGroup>
    </ProForm>
  );
};

export default Demo;
```