---
category: '@RxJS'
---

# useSubject

将 RxJS [`Subject`](https://rxjs.dev/guide/subject) 绑定到 `ref` 并双向传播值更改。

## 用法

<!-- TODO: 如果启用 twoslash，则导入 rxjs 错误 -->

```ts no-twoslash
import { useSubject } from '@vueuse/rxjs'
import { Subject } from 'rxjs'

const subject = new Subject()

// setup()
const subjectRef = useSubject(subject)

// 对 subjectRef.value 的更改将被推送到 subject
subjectRef.value = 'new value'

// subject 发出的值将更新 subjectRef
subject.next('from subject')
```

### 使用 BehaviorSubject

当使用 `BehaviorSubject` 时，返回的 ref 使用 subject 的当前值初始化，并且类型不包括 `undefined`：

```ts no-twoslash
import { useSubject } from '@vueuse/rxjs'
import { BehaviorSubject } from 'rxjs'

const subject = new BehaviorSubject('initial')

// setup()
const subjectRef = useSubject(subject) // Ref<string>，而不是 Ref<string | undefined>
console.log(subjectRef.value) // 'initial'
```

### 错误处理

如果您想为可能出错的 Subject 添加自定义错误处理，可以提供一个可选的 `onError` 配置。如果没有此配置，RxJS 将把提供的 observable 中的任何错误视为"未处理的错误"，并将在新的调用堆栈中抛出并报告给 `window.onerror`（如果您恰好处于 node 中，则报告给 `process.on('error')`）。

```ts no-twoslash
import { useSubject } from '@vueuse/rxjs'
import { Subject } from 'rxjs'

const subject = new Subject()

// setup()
const subjectRef = useSubject(subject, {
  onError: (err) => {
    console.log(err.message) // "oops"
  },
},)
```

## 类型声明

```ts
export interface UseSubjectOptions<I = undefined> extends Omit<
  UseObservableOptions<I>,
  "initialValue"
> {}
export declare function useSubject<H>(
  subject: BehaviorSubject<H>,
  options?: UseSubjectOptions,
): Ref<H>
export declare function useSubject<H>(
  subject: Subject<H>,
  options?: UseSubjectOptions,
): Ref<H | undefined>
```
