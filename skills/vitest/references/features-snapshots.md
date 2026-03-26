---
name: snapshot-testing
description: 使用文件、内联和文件快照进行快照测试
---

# 快照测试

快照测试捕获输出并与存储的引用进行比较。

## 基本快照

```ts
import { expect, test } from 'vitest'

test('snapshot', () => {
  const result = generateOutput()
  expect(result).toMatchSnapshot()
})
```

第一次运行创建 `.snap` 文件:

```js
// __snapshots__/test.spec.ts.snap
exports['snapshot 1'] = `
{
  "id": 1,
  "name": "test"
}
`
```

## 内联快照

直接存储在测试文件中:

```ts
test('inline snapshot', () => {
  const data = { foo: 'bar' }
  expect(data).toMatchInlineSnapshot()
})
```

Vitest 更新测试文件:

```ts
test('inline snapshot', () => {
  const data = { foo: 'bar' }
  expect(data).toMatchInlineSnapshot(`
    {
      "foo": "bar",
    }
  `)
})
```

## 文件快照

与显式文件比较:

```ts
test('render html', async () => {
  const html = renderComponent()
  await expect(html).toMatchFileSnapshot('./expected/component.html')
})
```

## 快照提示

添加描述性提示:

```ts
test('multiple snapshots', () => {
  expect(header).toMatchSnapshot('header')
  expect(body).toMatchSnapshot('body content')
  expect(footer).toMatchSnapshot('footer')
})
```

## 对象形状匹配

匹配部分结构:

```ts
test('shape snapshot', () => {
  const data = { 
    id: Math.random(), 
    created: new Date(),
    name: 'test' 
  }
  
  expect(data).toMatchSnapshot({
    id: expect.any(Number),
    created: expect.any(Date),
  })
})
```

## 错误快照

```ts
test('error message', () => {
  expect(() => {
    throw new Error('Something went wrong')
  }).toThrowErrorMatchingSnapshot()
})

test('inline error', () => {
  expect(() => {
    throw new Error('Bad input')
  }).toThrowErrorMatchingInlineSnapshot(`[Error: Bad input]`)
})
```

## 更新快照

```bash
# 更新所有快照
vitest -u
vitest --update

# 在监视模式下,按 'u' 更新失败的快照
```

## 自定义序列化器

添加自定义快照格式化:

```ts
expect.addSnapshotSerializer({
  test(val) {
    return val && typeof val.toJSON === 'function'
  },
  serialize(val, config, indentation, depth, refs, printer) {
    return printer(val.toJSON(), config, indentation, depth, refs)
  },
})
```

或通过配置:

```ts
// vitest.config.ts
defineConfig({
  test: {
    snapshotSerializers: ['./my-serializer.ts'],
  },
})
```

## 快照格式选项

```ts
defineConfig({
  test: {
    snapshotFormat: {
      printBasicPrototype: false, // 不打印 Array/Object 原型
      escapeString: false,
    },
  },
})
```

## 并发测试快照

使用上下文的 expect:

```ts
test.concurrent('concurrent 1', async ({ expect }) => {
  expect(await getData()).toMatchSnapshot()
})

test.concurrent('concurrent 2', async ({ expect }) => {
  expect(await getOther()).toMatchSnapshot()
})
```

## 快照文件位置

默认: `__snapshots__/<test-file>.snap`

自定义:

```ts
defineConfig({
  test: {
    resolveSnapshotPath: (testPath, snapExtension) => {
      return testPath.replace('__tests__', '__snapshots__') + snapExtension
    },
  },
})
```

## 关键点

- 将快照文件提交到版本控制
- 在代码审查中审查快照更改
- 对一个测试中的多个快照使用提示
- 对大输出(HTML、JSON)使用 `toMatchFileSnapshot`
- 内联快照在测试文件中自动更新
- 对并发测试使用上下文的 `expect`

<!-- 
Source references:
- https://vitest.dev/guide/snapshot.html
- https://vitest.dev/api/expect.html#tomatchsnapshot
-->
