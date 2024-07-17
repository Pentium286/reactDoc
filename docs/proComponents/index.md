---
nav: 
  title: ProComponents 表单
  order: 2
group:
  title: 基础
  order: -1
---

# ProComponents 表单

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