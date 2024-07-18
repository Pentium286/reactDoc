---
group: 
  title: 高级表单
  order: 0
---

# ProForm 高级表单

ProForm 在原来的 Form 的基础上增加了一些语法糖和更多的布局设置，帮助我们快速地开发一个表单，同时添加了一些默认行为，让我们的表单默认好用。

引入方法  `import { ProForm } from '@ant-design/pro-components';`

分步表单、Modal 表单、Drawer 表单、查询表单、轻量筛选等多种 layout 可以覆盖大部分的使用场景，让我们脱离复杂而且繁琐的表单布局工作，用更少的代码完成更多的功能。

- 如果想要设置默认值，请使用 `initialValues`，任何直接使用组件 `value` 和 `onChange` 的方式都有可能导致值绑定失效
- 如果想要表单联动或者做一些依赖，可以使用 render props 模式，ProFormDependency 绝对是最好的选择
- ProForm 的 onFinish 与 antd 的 Form 不同，支持 Promise，如果你正常返回会自动为你设置按钮的加载效果
- 如果想要监听某个值，建议使用 `onValuesChange`。保持单向的数据流无论对开发者还是维护者都大有裨益
- ProForm 没有黑科技，只是 antd 的 Form 的封装，如果要使用自定义的组件可以用 Form.Item 包裹后使用，支持混用

```tsx | pure
// 设置整体默认值
<ProForm initialValues={obj} />

// 设置单个控件的
<ProForm
 onValuesChange={(changeValues) => console.log(changeValues)}
>
  <ProFormText initialValue="prop"/>
</ProForm>

// 相互依赖的组件联动
<ProForm>
  <Form.Item noStyle shouldUpdate>
    {(form) => {
      return (
        <ProFormSelect
          options={[
            {
              value: "chapter",
              label: "盖章后生效",
            },
          ]}
          width="md"
          name="useMode"
          label={`与${form.getFieldValue("name")}合同约定生效方式`}
        />
      );
    }}
  </Form.Item>
</ProForm>;


// 使用自定义组件
<ProForm>
  <Form.Item name="switch" label="Switch" valuePropName="checked">
    <Switch />
  </Form.Item>
</ProForm>
```

## 何时使用 ProForm

当你想快速实现一个表单但不想花太多时间去布局时 ProForm 是最好的选择。

ProForm 是基于 antd Form 的可降级封装，与 antd 功能完全对齐，但是在其之上还增加一些预设行为和多种布局。这些布局之间可以无缝切换，并且拥有公共的 API。

| 布局 | 使用场景 |
| --- | --- |
| [ProForm](/components/form#proform) | 标准 Form，增加了 `onFinish` 中自动 `loading` 和根据 `request` 自动获取默认值的功能。 |
| [ModalForm\|DrawerForm](/components/modal-form) | 在 ProForm 的基础上增加了 `trigger` ，无需维护 `visible` 状态。 |
| [QueryFilter](/components/query-filter) | 一般用于作为筛选表单，需要配合其他数据展示组件使用。 |
| [LightFilter](/components/query-filter) | 一般用于作为行内内置的筛选，比如卡片操作栏和表格操作栏。 |
| [StepsForm](/components/steps-form) | 分步表单，需要配置 StepForm 使用。 |

```tsx
import {
  AlipayCircleOutlined,
  LockOutlined,
  PlusOutlined,
  TaobaoCircleOutlined,
  UserOutlined,
  WeiboCircleOutlined,
} from '@ant-design/icons';
import {
  DrawerForm,
  LightFilter,
  LoginForm,
  ModalForm,
  ProForm,
  ProFormDateRangePicker,
  ProFormRadio,
  ProFormSelect,
  ProFormText,
  QueryFilter,
  StepsForm,
} from '@ant-design/pro-components';
import { Button, Space, message } from 'antd';
import { useState } from 'react';

const iconStyles = {
  marginInlineStart: '16px',
  color: 'rgba(0, 0, 0, 0.2)',
  fontSize: '24px',
  verticalAlign: 'middle',
  cursor: 'pointer',
};

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

export default () => {
  const Components = {
    ProForm,
    ModalForm,
    DrawerForm,
    QueryFilter,
    LightFilter,
    StepsForm,
    LoginForm,
  };
  const [type, setType] = useState<keyof typeof Components>('ProForm');

  if (type === 'StepsForm') {
    return (
      <>
        <ProFormRadio.Group
          style={{
            margin: 16,
          }}
          radioType="button"
          fieldProps={{
            value: type,
            onChange: (e) => setType(e.target.value),
          }}
          options={[
            'LightFilter',
            'ProForm',
            'ModalForm',
            'DrawerForm',
            'QueryFilter',
            'StepsForm',
            'LoginForm',
          ]}
        />
        <StepsForm
          onFinish={async (values: any) => {
            await waitTime(2000);
            console.log(values);
            message.success('提交成功');
          }}
        >
          <StepsForm.StepForm title="第一步">
            <ProForm.Group>
              <ProFormText
                width="md"
                name="name"
                label="签约客户名称"
                tooltip="最长为 24 位"
                placeholder="请输入名称"
              />
              <ProFormText
                width="md"
                name="company"
                label="我方公司名称"
                placeholder="请输入名称"
              />
            </ProForm.Group>
            <ProForm.Group>
              <ProFormText
                name={['contract', 'name']}
                width="md"
                label="合同名称"
                placeholder="请输入名称"
              />
              <ProFormDateRangePicker
                width="md"
                name={['contract', 'createTime']}
                label="合同生效时间"
              />
            </ProForm.Group>
          </StepsForm.StepForm>
          <StepsForm.StepForm title="第二步">
            <ProForm.Group>
              <ProFormSelect
                options={[
                  {
                    value: 'chapter',
                    label: '盖章后生效',
                  },
                ]}
                readonly
                width="xs"
                name="useMode"
                label="合同约定生效方式"
              />
              <ProFormSelect
                width="xs"
                options={[
                  {
                    value: 'time',
                    label: '履行完终止',
                  },
                ]}
                name="unusedMode"
                label="合同约定失效效方式"
              />
            </ProForm.Group>
          </StepsForm.StepForm>
          <StepsForm.StepForm title="第二步">
            <ProFormText width="sm" name="id" label="主合同编号" />
            <ProFormText
              name="project"
              width="md"
              disabled
              label="项目名称"
              initialValue="xxxx项目"
            />
            <ProFormText
              width="xs"
              name="mangerName"
              disabled
              label="商务经理"
              initialValue="启途"
            />
          </StepsForm.StepForm>
        </StepsForm>
      </>
    );
  }

  const FormComponents = Components[type as 'LoginForm'];

  if (type === 'LoginForm') {
    return (
      <>
        <ProFormRadio.Group
          style={{
            margin: 16,
          }}
          radioType="button"
          fieldProps={{
            value: type,
            onChange: (e) => setType(e.target.value),
          }}
          options={[
            'LightFilter',
            'ProForm',
            'ModalForm',
            'DrawerForm',
            'QueryFilter',
            'StepsForm',
            'LoginForm',
          ]}
        />
        <FormComponents
          title="Github"
          subTitle="全球最大的代码托管平台"
          actions={
            <Space>
              其他登录方式
              <AlipayCircleOutlined style={iconStyles} />
              <TaobaoCircleOutlined style={iconStyles} />
              <WeiboCircleOutlined style={iconStyles} />
            </Space>
          }
        >
          <ProFormText
            name="username"
            fieldProps={{
              size: 'large',
              prefix: <UserOutlined className={'prefixIcon'} />,
            }}
            placeholder={'用户名: admin or user'}
            rules={[
              {
                required: true,
                message: '请输入用户名!',
              },
            ]}
          />
          <ProFormText.Password
            name="password"
            fieldProps={{
              size: 'large',
              prefix: <LockOutlined className={'prefixIcon'} />,
            }}
            placeholder={'密码: ant.design'}
            rules={[
              {
                required: true,
                message: '请输入密码！',
              },
            ]}
          />
        </FormComponents>
      </>
    );
  }

  return (
    <>
      <ProFormRadio.Group
        style={{
          margin: 16,
        }}
        radioType="button"
        fieldProps={{
          value: type,
          onChange: (e) => setType(e.target.value),
        }}
        options={[
          'LightFilter',
          'ProForm',
          'ModalForm',
          'DrawerForm',
          'QueryFilter',
          'StepsForm',
          'LoginForm',
        ]}
      />
      <div
        style={{
          margin: 24,
        }}
      >
        <FormComponents
          // @ts-ignore
          labelWidth="auto"
          trigger={
            <Button type="primary">
              <PlusOutlined />
              新建表单
            </Button>
          }
          onFinish={async (values: any) => {
            await waitTime(2000);
            console.log(values);
            message.success('提交成功');
          }}
          initialValues={{
            name: '蚂蚁设计有限公司',
            useMode: 'chapter',
          }}
        >
          <ProForm.Group>
            <ProFormText
              width="md"
              name="name"
              label="签约客户名称"
              tooltip="最长为 24 位"
              placeholder="请输入名称"
            />
            <ProFormText
              width="md"
              name="company"
              label="我方公司名称"
              placeholder="请输入名称"
            />
          </ProForm.Group>
          <ProForm.Group>
            <ProFormText
              name={['contract', 'name']}
              width="md"
              label="合同名称"
              placeholder="请输入名称"
            />
            <ProFormDateRangePicker
              width="md"
              name={['contract', 'createTime']}
              label="合同生效时间"
            />
          </ProForm.Group>
          <ProForm.Group>
            <ProFormSelect
              options={[
                {
                  value: 'chapter',
                  label: '盖章后生效',
                },
              ]}
              readonly
              width="xs"
              name="useMode"
              label="合同约定生效方式"
            />
            <ProFormSelect
              width="xs"
              options={[
                {
                  value: 'time',
                  label: '履行完终止',
                },
              ]}
              name="unusedMode"
              label="合同约定失效效方式"
            />
          </ProForm.Group>
          <ProFormText width="sm" name="id" label="主合同编号" />
          <ProFormText
            name="project"
            width="md"
            disabled
            label="项目名称"
            initialValue="xxxx项目"
          />
          <ProFormText
            width="xs"
            name="mangerName"
            disabled
            label="商务经理"
            initialValue="启途"
          />
        </FormComponents>
      </div>
    </>
  );
};
```

## 数据转化

很多时候组件需要的数据和后端需要的数据之间不能完全匹配，ProForm 为了解决这个问题提供了 `transform` 和 `convertValue` 两个 API 来处理这种情况。

### convertValue 前置转化

convertValue 发生在组件获得数据之前，一般是后端直接给前端的数据，有时需要精加工一下。

```tsx | pure
   export type SearchConvertKeyFn =
    (value: any, field: NamePath)=>string | boolean | Record<string, any>;
  /**
   * @name 获取时转化值，一般用于将数据格式化为组件接收的格式
   * @param value 字段的值
   * @param namePath 字段的name
   * @returns 字段新的值
   *
   * @example a,b => [a,b]
   * convertValue:(value,namePath)=>value.split(",")
   * @example string =>json
   * convertValue:(value,namePath)=>JSON.parse(value)
   * @example number =>date
   * convertValue:(value,namePath)=>Moment(value)
   * @example YYYY-MM-DD => date
   * convertValue:(value,namePath)=>Moment(value,"YYYY-MM-DD")
   * @example  string => object
   * convertValue:(value,namePath)=>({value,label:value})
   */
  convertValue?: SearchConvertKeyFn;
```

### transform 提交时转化

transform 发生在提交的时候，一般来说都是吐给后端的存在数据库里的数据。

为了方便大家使用，`ProFormDependency` 和 `formRef` 都支持了 `transform`，可以获取到被转化后的值。

```tsx | pure
<ProFormDependency>
  {(value, form) => {
    // value 被 transform转化之后的值
    // form 当前的formRef，可以获取未转化的值
    return ReactNode;
  }}
</ProFormDependency>
```

formRef 内置了几个方法来获取转化之后的值，这也是相比 antd 的 Form 新增的功能，详细可以看 ProFormInstance 的类型定义。

```tsx | pure
  /** 获取被 ProForm 格式化后的所有数据  */
  getFieldsFormatValue?: (nameList?: true) => T;
  /** 获取格式化之后的单个数据 */
  getFieldFormatValue?: (nameList?: NamePath) => T;
  /** 获取格式化之后的单个数据 */
  getFieldFormatValueObject?: (nameList?: NamePath) => T;
  /** 验字段后返回格式化之后的所有数据*/
  validateFieldsReturnFormatValue?: (nameList?: NamePath[]) => Promise<T>;
```

```tsx | pure
  export type SearchTransformKeyFn = (
    value: any,
    namePath: string,
    allValues: any,
  ) => string | Record<string, any>;

  /**
   * @name 提交时转化值，一般用于将值转化为提交的数据
   * @param value 字段的值
   * @param namePath 字段的name
   * @param allValues 所有的字段
   * @returns 字段新的值，如果返回对象，会和所有值深度 merge 一次
   *
   * @example {name:[a,b] => {name:a,b }
   * transform: (value,namePath,allValues)=> value.join(",")
   * @example {name: string => { newName:string }
   * transform: (value,namePath,allValues)=> { newName:value }
   * @example {name:moment} => {name:string
   * transform: (value,namePath,allValues)=> value.format("YYYY-MM-DD")
   * @example {name:moment}=> {name:时间戳}
   * transform: (value,namePath,allValues)=> value.valueOf()
   * @example {name:{value,label}} => { name:string}
   * transform: (value,namePath,allValues)=> value.value
   * @example {name:{value,label}} => { valueName,labelName  }
   * transform: (value)=>{valueName:value.value,labelName:value.name}
   */
  transform?: SearchTransformKeyFn;
```

## 代码示例

```tsx
import type { ProFormInstance } from '@ant-design/pro-components';
import {
  ProForm,
  ProFormCascader,
  ProFormDatePicker,
  ProFormDateRangePicker,
  ProFormDigit,
  ProFormList,
  ProFormMoney,
  ProFormSelect,
  ProFormText,
  ProFormTextArea,
  ProFormTreeSelect,
} from '@ant-design/pro-components';
import { TreeSelect, message } from 'antd';
import moment from 'dayjs';
import { useRef } from 'react';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

const treeData = [
  {
    title: 'Node1',
    value: '0-0',
    key: '0-0',
    children: [
      {
        title: 'Child Node1',
        value: '0-0-0',
        key: '0-0-0',
      },
    ],
  },
  {
    title: 'Node2',
    value: '0-1',
    key: '0-1',
    children: [
      {
        title: 'Child Node3',
        value: '0-1-0',
        key: '0-1-0',
      },
      {
        title: 'Child Node4',
        value: '0-1-1',
        key: '0-1-1',
      },
      {
        title: 'Child Node5',
        value: '0-1-2',
        key: '0-1-2',
      },
    ],
  },
];

export default () => {
  const formRef = useRef<
    ProFormInstance<{
      name: string;
      company?: string;
      useMode?: string;
    }>
  >();
  return (
    <ProForm<{
      name: string;
      company?: string;
      useMode?: string;
    }>
      onFinish={async (values) => {
        await waitTime(2000);
        console.log(values);
        const val1 = await formRef.current?.validateFields();
        console.log('validateFields:', val1);
        const val2 = await formRef.current?.validateFieldsReturnFormatValue?.();
        console.log('validateFieldsReturnFormatValue:', val2);
        message.success('提交成功');
      }}
      formRef={formRef}
      params={{ id: '100' }}
      formKey="base-form-use-demo"
      dateFormatter={(value, valueType) => {
        console.log('---->', value, valueType);
        return value.format('YYYY/MM/DD HH:mm:ss');
      }}
      request={async () => {
        await waitTime(1500);
        return {
          name: '蚂蚁设计有限公司',
          useMode: 'chapter',
        };
      }}
      autoFocusFirstInput
    >
      <ProForm.Group>
        <ProFormText
          width="md"
          name="name"
          required
          dependencies={[['contract', 'name']]}
          addonBefore={<a>客户名称应该怎么获得？</a>}
          addonAfter={<a>点击查看更多</a>}
          label="签约客户名称"
          tooltip="最长为 24 位"
          placeholder="请输入名称"
          rules={[{ required: true, message: '这是必填项' }]}
        />
        <ProFormText
          width="md"
          name="company"
          label="我方公司名称"
          placeholder="请输入名称"
        />
      </ProForm.Group>
      <ProForm.Group>
        <ProFormDigit name="count" label="人数" width="lg" />
      </ProForm.Group>
      <ProForm.Group>
        <ProFormText
          name={['contract', 'name']}
          width="md"
          label="合同名称"
          placeholder="请输入名称"
        />
        <ProFormDateRangePicker
          width="md"
          name={['contract', 'createTime']}
          label="合同生效时间"
        />
      </ProForm.Group>
      <ProForm.Group>
        <ProFormSelect
          options={[
            {
              value: 'chapter',
              label: '盖章后生效',
            },
          ]}
          readonly
          width="xs"
          cacheForSwr
          name="useMode"
          label="合同约定生效方式"
        />
        <ProFormSelect.SearchSelect
          width="xs"
          options={[
            {
              value: 'time',
              label: '履行完终止',
              type: 'time',
              options: [
                {
                  value: 'time1',
                  label: '履行完终止1',
                },
                {
                  value: 'time2',
                  label: '履行完终止2',
                },
              ],
            },
          ]}
          name="unusedMode"
          label="合同约定失效方式"
        />
        <ProFormMoney
          width="md"
          name="money"
          label="合同约定金额"
          fieldProps={{
            numberPopoverRender: true,
          }}
        />
      </ProForm.Group>
      <ProFormText width="sm" name="id" label="主合同编号" />
      <ProFormText
        name="project"
        width="md"
        disabled
        label="项目名称"
        initialValue="xxxx项目"
      />
      <ProFormTextArea
        colProps={{ span: 24 }}
        name="address"
        label="详细的工作地址或家庭住址"
      />
      <ProFormText
        width="xs"
        name="mangerName"
        disabled
        label="商务经理"
        initialValue="启途"
      />
      <ProFormCascader
        width="md"
        request={async () => [
          {
            value: 'zhejiang',
            label: '浙江',
            children: [
              {
                value: 'hangzhou',
                label: '杭州',
                children: [
                  {
                    value: 'xihu',
                    label: '西湖',
                  },
                ],
              },
            ],
          },
          {
            value: 'jiangsu',
            label: 'Jiangsu',
            children: [
              {
                value: 'nanjing',
                label: 'Nanjing',
                children: [
                  {
                    value: 'zhonghuamen',
                    label: 'Zhong Hua Men',
                  },
                ],
              },
            ],
          },
        ]}
        name="areaList"
        label="区域"
        initialValue={['zhejiang', 'hangzhou', 'xihu']}
        addonAfter={'qixian'}
      />
      <ProFormTreeSelect
        initialValue={['0-0-0']}
        label="树形下拉选择器"
        width={600}
        fieldProps={{
          fieldNames: {
            label: 'title',
          },
          treeData,
          treeCheckable: true,
          showCheckedStrategy: TreeSelect.SHOW_PARENT,
          placeholder: 'Please select',
        }}
      />
      <ProFormDatePicker
        name="date"
        transform={(value) => {
          return {
            date: moment(value).unix(),
          };
        }}
      />
      <ProFormList name="datas">
        {() => {
          return (
            <>
              <ProFormDatePicker
                name="date"
                transform={(value) => {
                  return {
                    date: moment(value).unix(),
                  };
                }}
              />

              <ProFormList name="innerDatas">
                {() => {
                  return (
                    <>
                      <ProFormDatePicker
                        name="date"
                        transform={(value) => {
                          return {
                            date: moment(value).unix(),
                          };
                        }}
                      />
                      <ProFormList name="innerDatas">
                        {() => {
                          return (
                            <>
                              <ProFormDatePicker
                                name="date"
                                transform={(value) => {
                                  return {
                                    date: moment(value).unix(),
                                  };
                                }}
                              />
                              <ProFormList name="innerDatas">
                                {() => {
                                  return (
                                    <>
                                      <ProFormDatePicker
                                        name="date"
                                        transform={(value) => {
                                          return {
                                            date: moment(value).unix(),
                                          };
                                        }}
                                      />
                                    </>
                                  );
                                }}
                              </ProFormList>
                            </>
                          );
                        }}
                      </ProFormList>
                    </>
                  );
                }}
              </ProFormList>
            </>
          );
        }}
      </ProFormList>
    </ProForm>
  );
};
```

### 标签与表单项布局

除了 `LightFilter` 和 `QueryFilter` 这样固定布局的表单样式，其他表单布局支持配置与 `antd` 一致的三种布局方式。

```tsx
import { ProForm, ProFormRadio, ProFormText } from '@ant-design/pro-components';
import { Col, Row, Space, message } from 'antd';
import { useState } from 'react';

type LayoutType = Parameters<typeof ProForm>[0]['layout'];
const LAYOUT_TYPE_HORIZONTAL = 'horizontal';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

export default () => {
  const [formLayoutType, setFormLayoutType] = useState<LayoutType>(
    LAYOUT_TYPE_HORIZONTAL,
  );

  const formItemLayout =
    formLayoutType === LAYOUT_TYPE_HORIZONTAL
      ? {
          labelCol: { span: 4 },
          wrapperCol: { span: 14 },
        }
      : null;

  return (
    <ProForm<{
      name: string;
      company?: string;
      useMode?: string;
    }>
      {...formItemLayout}
      layout={formLayoutType}
      submitter={{
        render: (props, doms) => {
          return formLayoutType === LAYOUT_TYPE_HORIZONTAL ? (
            <Row>
              <Col span={14} offset={4}>
                <Space>{doms}</Space>
              </Col>
            </Row>
          ) : (
            doms
          );
        },
      }}
      onFinish={async (values) => {
        await waitTime(2000);
        console.log(values);
        message.success('提交成功');
      }}
      params={{}}
      request={async () => {
        await waitTime(100);
        return {
          name: '蚂蚁设计有限公司',
          useMode: 'chapter',
        };
      }}
    >
      <ProFormRadio.Group
        style={{
          margin: 16,
        }}
        label="标签布局"
        radioType="button"
        fieldProps={{
          value: formLayoutType,
          onChange: (e) => setFormLayoutType(e.target.value),
        }}
        options={['horizontal', 'vertical', 'inline']}
      />
      <ProFormText
        width="md"
        name="name"
        label="签约客户名称"
        tooltip="最长为 24 位"
        placeholder="请输入名称"
      />
      <ProFormText
        width="md"
        name="company"
        label="我方公司名称"
        placeholder="请输入名称"
      />
      <ProFormText
        name={['contract', 'name']}
        width="md"
        label="合同名称"
        placeholder="请输入名称"
      />
    </ProForm>
  );
};
```

### 栅格化布局

同时支持在 `ProForm`, `SchemaForm`, `ModalForm`, `DrawerForm`, `StepsForm` 中使用

#### 基础用法

```tsx
import {
  ProForm,
  ProFormDatePicker,
  ProFormDateRangePicker,
  ProFormDigit,
  ProFormRadio,
  ProFormSelect,
  ProFormSwitch,
  ProFormText,
  ProFormTextArea,
} from '@ant-design/pro-components';
import { Col, Row, Space, message } from 'antd';
import type { FormLayout } from 'antd/lib/form/Form';
import { useState } from 'react';

const LAYOUT_TYPE_HORIZONTAL = 'horizontal';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

export default () => {
  const [formLayoutType, setFormLayoutType] = useState<FormLayout>(
    LAYOUT_TYPE_HORIZONTAL,
  );

  const [grid, setGrid] = useState(true);

  return (
    <ProForm<{
      name: string;
      company?: string;
      useMode?: string;
    }>
      layout={formLayoutType}
      grid={grid}
      rowProps={{
        gutter: [16, formLayoutType === 'inline' ? 16 : 0],
      }}
      submitter={{
        render: (props, doms) => {
          return formLayoutType === LAYOUT_TYPE_HORIZONTAL ? (
            <Row>
              <Col span={14} offset={4}>
                <Space>{doms}</Space>
              </Col>
            </Row>
          ) : (
            doms
          );
        },
      }}
      onFinish={async (values) => {
        await waitTime(2000);
        console.log(values);
        message.success('提交成功');
      }}
      params={{}}
      request={async () => {
        await waitTime(100);
        return {
          name: '蚂蚁设计有限公司',
          useMode: 'chapter',
        };
      }}
    >
      <ProFormRadio.Group
        label="标签布局"
        radioType="button"
        fieldProps={{
          value: formLayoutType,
          onChange: (e) => setFormLayoutType(e.target.value),
        }}
        colProps={{
          span: 20,
        }}
        options={['horizontal', 'vertical', 'inline']}
      />
      <ProFormSwitch
        colProps={{
          span: 4,
        }}
        fieldProps={{
          onChange: setGrid,
        }}
        initialValue={true}
        label="grid开关"
        name="grid"
      />
      <ProFormText
        name="name"
        label="标题"
        tooltip="最长为 24 位"
        placeholder="请输入名称"
      />
      <ProFormText colProps={{ md: 12, xl: 8 }} name="company" label="姓名" />
      <ProFormDigit colProps={{ md: 12, xl: 8 }} name="phone" label="电话" />
      <ProFormText colProps={{ md: 12, xl: 8 }} name="email" label="邮箱" />
      <ProFormTextArea
        colProps={{ span: 24 }}
        name="address"
        label="详细的工作地址或家庭住址"
      />
      <ProFormDatePicker
        colProps={{ xl: 8, md: 12 }}
        label="入职日期"
        name="date"
      />
      <ProFormDateRangePicker
        colProps={{ xl: 8, md: 12 }}
        label="工作周期"
        name="dateRange"
      />
      <ProFormSelect
        colProps={{ xl: 8, md: 12 }}
        label="职位"
        name="level"
        valueEnum={{
          1: 'front end',
          2: 'back end',
          3: 'full stack',
        }}
      />
    </ProForm>
  );
};
```

#### 表单联动

```tsx
import {
  ProForm,
  ProFormDependency,
  ProFormSelect,
  ProFormText,
} from '@ant-design/pro-components';
import { message } from 'antd';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

export default () => {
  return (
    <ProForm
      onFinish={async (values) => {
        await waitTime(2000);
        console.log(values);
        message.success('提交成功');
      }}
      initialValues={{
        name: '蚂蚁设计有限公司',
        name2: '蚂蚁设计集团',
        useMode: 'chapter',
      }}
    >
      <ProFormText
        width="md"
        name="name"
        label="签约客户名称"
        tooltip="最长为 24 位"
        placeholder="请输入名称"
      />
      <ProFormText
        width="md"
        name={['name2', 'text']}
        label="签约客户名称"
        tooltip="最长为 24 位"
        placeholder="请输入名称"
      />
      {/*  ProFormDependency 会自动注入并且 进行 shouldUpdate 的比对  */}
      <ProFormDependency name={['name', ['name2', 'text']]}>
        {({ name, name2 }) => {
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
              label={`与《${name || ''}》 与 《${
                name2?.text || ''
              }》合同约定生效方式`}
            />
          );
        }}
      </ProFormDependency>
      {/* noStyle shouldUpdate 是必选的，写了 name 就会失效 */}
      <ProForm.Item noStyle shouldUpdate>
        {(form) => {
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
              label={`与《${form.getFieldValue('name')}》合同约定生效方式`}
            />
          );
        }}
      </ProForm.Item>
    </ProForm>
  );
};
```

#### 表单方法调用

你可以通过 `formRef` 获取到表单实例的引用，通过引用可以调用表单方法实现表单重置，设置表单，获取表单值等功能。

```tsx
import type { ProFormInstance } from '@ant-design/pro-components';
import {
  ProForm,
  ProFormDatePicker,
  ProFormText,
} from '@ant-design/pro-components';
import { Button, message } from 'antd';
import dayjs from 'dayjs';
import { useRef } from 'react';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

export default () => {
  const formRef = useRef<ProFormInstance>();
  const onFill = () => {
    formRef?.current?.setFieldsValue({
      name: '张三',
      company: '蚂蚁金服',
    });
  };

  const getCompanyName = () => {
    message.info(`公司名称为 "${formRef?.current?.getFieldValue('company')}"`);
  };

  const getFormatValues = () => {
    console.log(
      '格式化后的所有数据：',
      formRef.current?.getFieldsFormatValue?.(),
    );
  };

  const validateAndGetFormatValue = () => {
    formRef.current?.validateFieldsReturnFormatValue?.().then((values) => {
      console.log('校验表单并返回格式化后的所有数据：', values);
    });
  };

  return (
    <ProForm
      title="新建表单"
      formRef={formRef}
      submitter={{
        render: (props, doms) => {
          return [
            ...doms,
            <Button htmlType="button" onClick={onFill} key="edit">
              一键填写
            </Button>,
            <Button htmlType="button" onClick={getCompanyName} key="read">
              读取公司
            </Button>,
            <Button.Group key="refs" style={{ display: 'block' }}>
              <Button htmlType="button" onClick={getFormatValues} key="format">
                获取格式化后的所有数据
              </Button>
              <Button
                htmlType="button"
                onClick={validateAndGetFormatValue}
                key="format2"
              >
                校验表单并返回格式化后的所有数据
              </Button>
            </Button.Group>,
          ];
        },
      }}
      onFinish={async (values) => {
        await waitTime(2000);
        console.log(values);
        message.success('提交成功');
        return true;
      }}
    >
      <ProFormText
        width="md"
        name="name"
        label="签约客户名称"
        tooltip="最长为 24 位"
        placeholder="请输入名称"
      />

      <ProFormText
        width="md"
        name="company"
        label="我方公司名称"
        placeholder="请输入名称"
      />
      <ProFormDatePicker name="date" initialValue={dayjs('2021-08-09')} />
    </ProForm>
  );
};
```

### 同步提交结果到 url

打开时也会把 url 的参数设置为默认值，支持 transform, 但是要注意字段的映射。

```tsx
import {
  ProForm,
  ProFormDatePicker,
  ProFormDateRangePicker,
  ProFormSelect,
} from '@ant-design/pro-components';
import { message } from 'antd';

export default () => {
  return (
    <ProForm
      onFinish={async () => {
        message.success('提交成功');
      }}
      syncToUrl={(values, type) => {
        if (type === 'get') {
          // 为了配合 transform
          // startTime 和 endTime 拼成 createTimeRanger
          return {
            ...values,
            createTimeRanger:
              values.startTime || values.endTime
                ? [values.startTime, values.endTime]
                : undefined,
          };
        }
        // expirationTime 不同步到 url
        return {
          ...values,
          expirationTime: undefined,
        };
      }}
      initialValues={{
        name: '蚂蚁设计有限公司',
        useMode: 'chapter',
      }}
      autoFocusFirstInput
    >
      <ProFormSelect
        options={[
          {
            value: 'chapter',
            label: '盖章后生效',
          },
        ]}
        width="sm"
        name="useMode"
        label="合同约定生效方式"
      />
      <ProFormDateRangePicker
        transform={(values) => {
          return {
            startTime: values ? values[0] : undefined,
            endTime: values ? values[1] : undefined,
          };
        }}
        width="md"
        name="createTimeRanger"
        label="合同生效时间"
      />
      <ProFormDatePicker
        width="md"
        name="expirationTime"
        label="合同失效时间"
      />
    </ProForm>
  );
};
```

#### 金额

```tsx
import type { ProFormInstance } from '@ant-design/pro-components';
import {
  ProForm,
  ProFormMoney,
  ProFormSwitch,
} from '@ant-design/pro-components';
import { message } from 'antd';
import { useRef, useState } from 'react';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

export default () => {
  const formRef = useRef<
    ProFormInstance<{
      name: string;
      company?: string;
      useMode?: string;
    }>
  >();

  const [readonly, setReadonly] = useState(false);
  return (
    <>
      <ProFormSwitch
        checkedChildren="开启"
        unCheckedChildren="关闭"
        label="是否只读"
        fieldProps={{
          onChange: setReadonly,
        }}
      />
      <ProForm<{
        name: string;
        company?: string;
        useMode?: string;
      }>
        onFinish={async (values) => {
          await waitTime(2000);
          console.log(values);
          const val1 = await formRef.current?.validateFields();
          console.log('validateFields:', val1);
          const val2 =
            await formRef.current?.validateFieldsReturnFormatValue?.();
          console.log('validateFieldsReturnFormatValue:', val2);
          message.success('提交成功');
        }}
        formRef={formRef}
        params={{ id: '100' }}
        formKey="base-form-use-demo"
        readonly={readonly}
        request={async () => {
          await waitTime(100);
          return {
            name: '蚂蚁设计有限公司',
            useMode: 'chapter',
          };
        }}
        autoFocusFirstInput
      >
        <ProFormMoney
          label="不显示符号"
          name="amount0"
          fieldProps={{
            moneySymbol: false,
          }}
          locale="en-US"
          initialValue={22.22}
          min={0}
          width="lg"
        />
        <ProFormMoney
          label="宽度"
          name="amount1"
          locale="en-US"
          initialValue={22.22}
          min={0}
          width="lg"
        />
        <ProFormMoney
          label="限制金额最小为0"
          name="amount2"
          locale="en-US"
          initialValue={22.22}
          min={0}
          trigger="onBlur"
        />
        <ProFormMoney
          label="不限制金额大小"
          name="amount3"
          locale="en-GB"
          initialValue={22.22}
        />
        <ProFormMoney
          label="货币符号跟随全局国际化"
          name="amount4"
          initialValue={22.22}
        />
        <ProFormMoney
          label="货币符号指定为 ms-MY"
          name="amount-ms-My"
          locale="ms-MY"
          initialValue={-22.22}
        />
        <ProFormMoney
          label="货币符号指定为 zh-TW"
          name="amount-zh-TW"
          locale="zh-TW"
          initialValue={22.22}
        />
        <ProFormMoney
          label="自定义货币符号"
          name="amount5"
          initialValue={22.22}
          customSymbol="💰"
        />
        <ProFormMoney
          label="小数点精度"
          name="amount6"
          initialValue={2222222222.222222}
          fieldProps={{ precision: 2 }}
          customSymbol="💰"
        />
        <ProFormMoney
          label="小数点精度-0"
          name="amount6"
          initialValue={2222222222.222222}
          fieldProps={{ precision: 0 }}
          customSymbol="💰"
        />
      </ProForm>
    </>
  );
};
```

#### 固定页脚

```tsx | pure
import { SmileOutlined } from '@ant-design/icons';
import {
  FooterToolbar,
  PageContainer,
  ProForm,
  ProFormDateRangePicker,
  ProFormDigit,
  ProFormRadio,
  ProFormSelect,
  ProFormText,
  ProFormTextArea,
  ProFormUploadButton,
  ProLayout,
} from '@ant-design/pro-components';
import { Card } from 'antd';

export default () => {
  return (
   
      <PageContainer title="输入表单">
        <Card>
          <ProForm
            submitter={{
              render: (_, dom) => <FooterToolbar>{dom}</FooterToolbar>,
            }}
            onFinish={async (values) => console.log(values)}
          >
            <ProForm.Group>
              <ProFormText
                name="name"
                label="签约客户名称"
                tooltip="最长为 24 位"
                placeholder="请输入名称"
              />
              <ProFormText
                width="md"
                name="company"
                label="我方公司名称"
                placeholder="请输入名称"
              />
            </ProForm.Group>
            <ProForm.Group>
              <ProFormText
                name={['contract', 'name']}
                label="合同名称"
                placeholder="请输入名称"
              />
              <ProFormDateRangePicker
                name={['contract', 'createTime']}
                label="合同生效时间"
              />
            </ProForm.Group>
            <ProForm.Group>
              <ProFormSelect
                options={[
                  {
                    value: 'chapter',
                    label: '盖章后生效',
                  },
                ]}
                width="xs"
                name="chapter"
                label="合同约定生效方式"
              />
              <ProFormSelect
                width="xs"
                options={[
                  {
                    value: 'time',
                    label: '履行完终止',
                  },
                ]}
                name="unusedMode"
                label="合同约定失效效方式"
              />
            </ProForm.Group>
            <ProFormText width="sm" name="id" label="主合同编号" />
            <ProFormText
              name="project"
              disabled
              label="项目名称"
              initialValue="xxxx项目"
            />
            <ProFormText
              width="xs"
              name="mangerName"
              disabled
              label="商务经理"
              initialValue="启途"
            />
            <ProForm.Group>
              <ProFormSelect
                initialValue="money"
                options={[
                  {
                    value: 'money',
                    label: '确认金额',
                  },
                ]}
                width="xs"
                name="useMode"
                label="金额类型"
              />
              <ProFormSelect
                options={[
                  {
                    value: '6',
                    label: '6%',
                  },
                  {
                    value: '12',
                    label: '12%',
                  },
                ]}
                initialValue="6"
                width="xs"
                name="taxRate"
                label="税率"
              />
              <ProFormRadio.Group
                label="发票类型"
                name="invoiceType"
                initialValue="发票"
                options={['发票', '普票', '无票']}
              />
            </ProForm.Group>
            <ProFormUploadButton
              extra="支持扩展名：.jpg .zip .doc .wps"
              label="倒签报备附件"
              name="file"
              title="上传文件"
            />
            <ProFormDigit
              width="xs"
              name="num"
              label="合同份数"
              initialValue={5}
            />
            <ProFormTextArea width="xl" label="合同备注说明" name="remark" />
          </ProForm>
        </Card>
      </PageContainer>
  );
};
```

#### ProForm 和 EditableTable 同时使用

```tsx
import type { ProColumns } from '@ant-design/pro-components';
import {
  EditableProTable,
  ProForm,
  ProFormText,
} from '@ant-design/pro-components';
import { Input, message } from 'antd';
import React, { useState } from 'react';

const waitTime = (time: number = 100) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(true);
    }, time);
  });
};

type DataSourceType = {
  id: React.Key;
  title?: string;
  decs?: string;
  state?: string;
  created_at?: number;
  children?: DataSourceType[];
};

const defaultData: DataSourceType[] = [
  {
    id: 624748504,
    title: '活动名称一',
    decs: '这个活动真好玩',
    state: 'open',
    created_at: 1590486176000,
  },
  {
    id: 624691229,
    title: '活动名称二',
    decs: '这个活动真好玩',
    state: 'closed',
    created_at: 1590481162000,
  },
];

const columns: ProColumns<DataSourceType>[] = [
  {
    title: '活动名称',
    dataIndex: 'title',
    width: '30%',
  },
  {
    title: '状态',
    key: 'state',
    dataIndex: 'state',
    valueType: 'select',
    valueEnum: {
      all: { text: '全部', status: 'Default' },
      open: {
        text: '未解决',
        status: 'Error',
      },
      closed: {
        text: '已解决',
        status: 'Success',
      },
    },
  },
  {
    title: '描述',
    dataIndex: 'decs',
    renderFormItem: (_, { record }) => {
      console.log('----===>', record);
      return <Input addonBefore={(record as any)?.addonBefore} />;
    },
  },
  {
    title: '操作',
    valueType: 'option',
  },
];

export default () => {
  const [editableKeys, setEditableRowKeys] = useState<React.Key[]>(() =>
    defaultData.map((item) => item.id),
  );
  return (
    <ProForm<{
      name: string;
      company: string;
    }>
      grid
      onFinish={async (values) => {
        await waitTime(2000);
        console.log(values);
        message.success('提交成功');
      }}
      initialValues={{
        name: '蚂蚁设计有限公司',
        useMode: 'chapter',
      }}
    >
      <ProForm.Group>
        <ProFormText
          width="md"
          name="name"
          label="签约客户名称"
          tooltip="最长为 24 位"
          placeholder="请输入名称"
        />
        <ProFormText
          width="md"
          name="company"
          label="我方公司名称"
          placeholder="请输入名称"
        />
      </ProForm.Group>
      <ProFormText width="sm" name="id" label="主合同编号" />
      <ProForm.Item
        label="数组数据"
        name="dataSource"
        initialValue={defaultData}
        trigger="onValuesChange"
      >
        <EditableProTable<DataSourceType>
          rowKey="id"
          toolBarRender={false}
          columns={columns}
          recordCreatorProps={{
            newRecordType: 'dataSource',
            position: 'top',
            record: () => ({
              id: Date.now(),
              addonBefore: 'ccccccc',
              decs: 'testdesc',
            }),
          }}
          editable={{
            type: 'multiple',
            editableKeys,
            onChange: setEditableRowKeys,
            actionRender: (row, _, dom) => {
              return [dom.delete];
            },
          }}
        />
      </ProForm.Item>
    </ProForm>
  );
};
```