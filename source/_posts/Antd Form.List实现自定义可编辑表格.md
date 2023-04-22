---
title: Antd Form.List实现自定义可编辑表格
date: 2021-07-25 21:39:19
---
![Antd Form.List实现自定义可编辑表格](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/028c842058ce46ddb9fbf0fed268539f~tplv-k3u1fbpfcp-zoom-1.image)

### 一、前言

定制化较深的需求，往往需要可编辑表格来支持表格内的编辑，但是针对必填项校验以及自定义action动作，常规的EditableTable难以满足需要，而EditableProTable则因为定制化太深而不够灵活，所以我们需要更加灵活的方案来实现我们需要的效果，经试验，Form.List是个不错的实现思路。

### 二、针对需求

1.  表格内编辑
1.  动态新增行（自定义action按钮动作）
1.  最小维度为行的必填项校验
1.  灵活可扩展

### 三、实现方法

1.  尝试 在项目中，我碰到这样的需求，需要表格可编辑，且初始默认一行可编辑状态数据，点击行最后一列的add进行必填项校验，校验完成新增一行且把已编辑完的数据置为不可编辑状态，起初考虑的实现方式很简单，在外层包一个Form，columns里面的项再包裹Form.Item，至于是否可编辑状态，给表格的数据源加入一个editable字段用于判断是否编辑就行了，本以为这个方案可行，但实际上问题很多，如果要针对行进行校验，那显然每一个行都要和Form利用Form.Item的name属性进行绑定，这时候name是啥呢，没有层级划分，如果你给name的值单单为数据的字段名，那添加一行后下一行岂不是和第一行的name重复了？这会导致新增一行有初始数据，且初始数据是上一行已保存的数据，至于校验，每次新增行调用form.submit可以实现行的校验，但显然这种方案是不可行的。

意识到要让行数据对应Form的标志唯一，那么就考虑使用Form.List实现。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c55973895214035bd417eb514c1a2c5~tplv-k3u1fbpfcp-zoom-1.image)

2.  Form.List

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aefdb4a5f36b418495bd01695bd3b0ea~tplv-k3u1fbpfcp-zoom-1.image)

Form.List的基本用法参考antd官方文档，可见这个元素就是为了解决标志重复的问题而生的，它对初始的数组数据又做多了一层映射，这层映射对应一个数组，而数组内的元素则是存有原数据数组下标的对象。多说无益，看图。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a884bbe13984e62afea6f1ed9895ea8~tplv-k3u1fbpfcp-zoom-1.image)

有了这层映射，我们Form.Item的name便有了每行唯一的上级，于是我们就可以将表头配置的columns写成如下的形式。

```
render: (text, record) => {
        paidDetails[record.name])
        return paidDetails[record.name]?.editable ?
          <Form.Item
            rules={[{ required: true, message: undefined }]}
            name={[record.name, 'paymentTerm']}
            fieldKey={[record.fieldKey, 'paymentTerm']}
            style={{ marginBottom: '0px' }}
          >
            <Select placeholder={'Please enter'} style={{ width: 200 }} >
              {
                paymentTremOptions && paymentTremOptions.map((item) => {
                  return <Option value={item.value} key={item.value}>{item.label}</Option>
                })
              }
            </Select>
          </Form.Item>
          :                     (paymentTremOptions[paidDetails[record.name]?.paymentTerm - 1]?.label || '-')
          }
复制代码
```

到了这一步，本以为基本上顺利完成了，但是还是有些意想不到的问题，要实现校验后添加一行，利用Form.List本身的add根本不现实，因为我们还需要新添加的行可编辑而已校验的行不可编辑，也就是说要更改每行数据的editable字段，所以自然而然想到这里我们必须自定义新增行了。

然而一波未平一波又起，问题还是不断出现，以下是我整理的会遇到的细节问题：

-   columns内add动作调用form.submit进行校验，外层在Form的onFinish对状态数据进行处理（不可行，更新状态数据成功了，而fields还是不变，导致新增行失败）
-   既然fields不变，那每次进行判断fields长度与状态数据是否不匹配，然后再增加或减少一个数据（仍旧不可行，新增确实成功了，但是删除的时候会碰到特殊情况，比如删除最后一个直接报错，应该是手动处理fields不完善导致的，没有继续深究原因，但显然这条路走不通，fields既然是Form.Item内部维护的数据，那就不该由开发者手动去操作，否则会出现各种各样的问题）

3.  最终实现

最终问题在自定义add上，fields不能手动更改，但肯定有更改的方法，唯一能想到的途经就是通过form了，想想也是，我们给form添加初始值也不能直接更新状态数据就实现啊，都是利用setFieldsValue的，所以改用setFieldsValue后果然实现了。 还有一点，每次表格内行编辑完的数据如何保存，通过setFieldsValue只能新增行，但是行内的数据会丢失，丢失的原因很简单，我们在编辑的时候虽然Form临时保存了数据，但是添加行后，因为所有行的编辑状态会重新判断，所以表格会重新render，我们在编辑时又没有实时将变化更新到状态数据，而Form又拿状态数据作为初始值，自然导致重新render后行数和编辑状态都对了但是每行的字段数据却丢失了，知道问题，解决起来就很简单了，在setFieldsValue的同时也将数据更新到状态就可以了。

这些处理完后，接下来的问题就是何时进行校验何时进行新增，如果要在校验之后进行新增，那如何做到呢？方法不止一个，我们点击add的时候触发form.submit，就能在触发Form的onFinish之前进行校验，然后在onFinish内更新状态数据同时调用setFieldsValue更新表单数据就行了。 另一种方法也类似，无非是 把动作再做拆分，先利用 form.validateFields做校验，然后在其链式调用then中进行更新状态数据同时调用setFieldsValue更新表单数据就可以了。

添加实现了接下来就是删除，删除也很简单，不用进行校验，根据唯一标识过滤即可。

### 四、实现代码

1.  页面

```
 <Form form={form} initialValues={{ table: paidDetails }} onFinish={(data) => {
            setPaidDetails(data.table)
          }}>
            <Form.List name="table">
              {(fields) => {
                console.log(fields,'fields')
                return (
                  <div>
                    <Table
                      className={`xp-table x-table-large ${styles.paidDetailTable}`}
                      scroll={{ scrollToFirstRowOnChange: true, x: 'max-content' }}
                      dataSource={fields}
                      rowKey={'id'}
                      pagination={false}
                      columns={PaidDetailColumns(paidDetails, setPaidDetails, form, formatMessage)}
                    />
                  </div>
                );
              }
              }
            </Form.List>
          </Form>
复制代码
```

2.  columns

```
export const PaidDetailColumns = (
  paidDetails,
  setPaidDetails,
  form,
  formatMessage
) => {
  const columnsList = [    {      title: formatMessage({ id: 'app.No' }),      render: (_, record, index) => {        return index + 1      }    },    {      title: formatMessage({ id: 'repairSettlement.paymentTerm' }),      dataIndex: 'paymentTerm',      render: (text, record) => {        console.log(record, paymentTremOptions, paidDetails[record.name])
        return paidDetails[record.name]?.editable ?
          <Form.Item
            rules={[{ required: true, message: undefined }]}
            name={[record.name, 'paymentTerm']}
            fieldKey={[record.fieldKey, 'paymentTerm']}
            style={{ marginBottom: '0px' }}
          >
            <Select placeholder={'Please enter'} style={{ width: 200 }} >
              {
                paymentTremOptions && paymentTremOptions.map((item) => {
                  return <Option value={item.value} key={item.value}>{item.label}</Option>
                })
              }
            </Select>
          </Form.Item>
          : (paymentTremOptions[paidDetails[record.name]?.paymentTerm - 1]?.label || '-')
      }
    },
    {
      title: formatMessage({ id: 'repairSettlement.paymentReceivedAmount' }),
      dataIndex: 'receivableAmount',
      render: (text, record) => {
        return paidDetails[record.name]?.editable ?
          <Form.Item
            rules={[{ required: true, message: undefined }]}
            name={[record.name, 'receivableAmount']}
            fieldKey={[record.fieldKey, 'receivableAmount']}
            style={{ marginBottom: '0px' }}
          >
            <InputNumber placeholder={'Please enter'} style={{ width: 200 }} />
          </Form.Item>
          : (paidDetails[record.name]?.receivableAmount || '-')
      }
    },
    {
      title: formatMessage({ id: 'repairSettlement.remark' }),
      dataIndex: 'remark',
      render: (text, record) => {
        return paidDetails[record.name]?.editable ?
          <Form.Item
            name={[record.name, 'remark']}
            fieldKey={[record.fieldKey, 'remark']}
            style={{ marginBottom: '0px' }}
          >
            <Input placeholder={'Please enter'} style={{ width: 200 }} />
          </Form.Item>
          : (paidDetails[record.name]?.remark || '-')
      }
    },
    {
      title: formatMessage({ id: 'app.action' }),
      fixed: 'right',
      render: (text, record, _) => {
        return paidDetails[record.name]?.editable ?
          <a
            key="editable"
            onClick={() => {
              form.validateFields().then(() => {
                const list = form.getFieldValue('table');
                list.forEach((element: any, index: number) => {
                  list[index].editable = false
                });
                const nextList = list.concat([{                  editable: true,                  id: (Math.random() * 1000000).toFixed(0)                }]);
                console.log(nextList)
                form.submit()
                form.setFieldsValue({
                  table: nextList,
                });
              })
            }}
          >
            {formatMessage({ id: 'app.add' })}
          </a>
          :
          <a
            key="editable"
            onClick={() => {
              const list = form.getFieldValue('table').filter((element: any, index: number) => {
                return index !== record.name
              });
              const nextList = list.concat([]);
              setPaidDetails(nextList)
              form.setFieldsValue({
                table: nextList,
              });
            }}
          >
            {formatMessage({ id: 'app.remove' })}
          </a>
      }

    }
  ]
  return columnsList
}
复制代码
```

### 五、注意事项

1、 注意回显form.setFieldsValue({table: 状态数据}) 这里table对应Form.List的标识, 需要回显的时候Form的initialValue就不需要了