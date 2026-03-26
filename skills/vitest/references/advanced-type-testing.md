---
name: type-testing
description: 使用 expectTypeOf 和 assertType 测试 TypeScript 类型
---

# 类型测试

在不运行时执行的情况下测试 TypeScript 类型。

## 设置

类型测试使用 `.test-d.ts` 扩展名:

```ts
// math.test-d.ts
import { expectTypeOf } from 'vitest'
import { add } from './math'

test('add returns number', () => {
  expectTypeOf(add).returns.toBeNumber()
})
```

## 配置

```ts
defineConfig({
  test: {
    typecheck: {
      enabled: true,
      
      // 仅类型检查
      only: false,
      
      // 检查器: 'tsc' 或 'vue-tsc'
      checker: 'tsc',
      
      // 包含模式
      include: ['**/*.test-d.ts'],
      
      // 要使用的 tsconfig
      tsconfig: './tsconfig.json',
    },
  },
})
```

## expectTypeOf API

```ts
import { expectTypeOf } from 'vitest'

// 基本类型检查
expectTypeOf<string>().toBeString()
expectTypeOf<number>().toBeNumber()
expectTypeOf<boolean>().toBeBoolean()
expectTypeOf<null>().toBeNull()
expectTypeOf<undefined>().toBeUndefined()
expectTypeOf<void>().toBeVoid()
expectTypeOf<never>().toBeNever()
expectTypeOf<any>().toBeAny()
expectTypeOf<unknown>().toBeUnknown()
expectTypeOf<object>().toBeObject()
expectTypeOf<Function>().toBeFunction()
expectTypeOf<[]>().toBeArray()
expectTypeOf<symbol>().toBeSymbol()
```

## 值类型检查

```ts
const value = 'hello'
expectTypeOf(value).toBeString()

const obj = { name: 'test', count: 42 }
expectTypeOf(obj).toMatchTypeOf<{ name: string }>()
expectTypeOf(obj).toHaveProperty('name')
```

## 函数类型

```ts
function greet(name: string): string {
  return `Hello, ${name}`
}

expectTypeOf(greet).toBeFunction()
expectTypeOf(greet).parameters.toEqualTypeOf<[string]>()
expectTypeOf(greet).returns.toBeString()

// 参数检查
expectTypeOf(greet).parameter(0).toBeString()
```

## 对象类型

```ts
interface User {
  id: number
  name: string
  email?: string
}

expectTypeOf<User>().toHaveProperty('id')
expectTypeOf<User>().toHaveProperty('name').toBeString()

// 检查形状
expectTypeOf({ id: 1, name: 'test' }).toMatchTypeOf<User>()
```

## 相等性 vs 匹配

```ts
interface A { x: number }
interface B { x: number; y: string }

// toMatchTypeOf - 子集匹配
expectTypeOf<B>().toMatchTypeOf<A>()  // B extends A

// toEqualTypeOf - 精确匹配
expectTypeOf<A>().not.toEqualTypeOf<B>()  // 不是精确匹配
expectTypeOf<A>().toEqualTypeOf<{ x: number }>()  // 精确匹配
```

## 品牌类型

```ts
type UserId = number & { __brand: 'UserId' }
type PostId = number & { __brand: 'PostId' }

expectTypeOf<UserId>().not.toEqualTypeOf<PostId>()
expectTypeOf<UserId>().not.toEqualTypeOf<number>()
```

## 泛型类型

```ts
function identity<T>(value: T): T {
  return value
}

expectTypeOf(identity<string>).returns.toBeString()
expectTypeOf(identity<number>).returns.toBeNumber()
```

## 可空类型

```ts
type MaybeString = string | null | undefined

expectTypeOf<MaybeString>().toBeNullable()
expectTypeOf<string>().not.toBeNullable()
```

## assertType

断言值匹配类型(运行时无断言):

```ts
import { assertType } from 'vitest'

function getUser(): User | null {
  return { id: 1, name: 'test' }
}

test('returns user', () => {
  const result = getUser()
  
  // @ts-expect-error - 应该使类型检查失败
  assertType<string>(result)
  
  // 正确的类型
  assertType<User | null>(result)
})
```

## 使用 @ts-expect-error

测试代码产生类型错误:

```ts
test('rejects wrong types', () => {
  function requireString(s: string) {}
  
  // @ts-expect-error - number 不能赋值给 string
  requireString(123)
})
```

## 运行类型测试

```bash
# 运行类型测试
vitest typecheck

# 与单元测试一起运行
vitest --typecheck

# 仅类型测试
vitest --typecheck.only
```

## 混合测试文件

结合运行时和类型测试:

```ts
// user.test.ts
import { describe, expect, expectTypeOf, test } from 'vitest'
import { createUser } from './user'

describe('createUser', () => {
  test('runtime: creates user', () => {
    const user = createUser('John')
    expect(user.name).toBe('John')
  })

  test('types: returns User type', () => {
    expectTypeOf(createUser).returns.toMatchTypeOf<{ name: string }>()
  })
})
```

## 关键点

- 使用 `.test-d.ts` 进行仅类型测试
- `expectTypeOf` 用于类型断言
- `toMatchTypeOf` 用于子集匹配
- `toEqualTypeOf` 用于精确类型匹配
- 使用 `@ts-expect-error` 测试类型错误
- 使用 `vitest typecheck` 或 `--typecheck` 运行

<!-- 
Source references:
- https://vitest.dev/guide/testing-types.html
- https://vitest.dev/api/expect-typeof.html
-->
