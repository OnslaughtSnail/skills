# 来源与适用边界

## 主要来源

Robert C. Martin 等，《Clean Code: A Handbook of Agile Software Craftsmanship》英文第一版，ISBN 9780132350884。
[出版社书目与目录](https://www.informit.com/store/clean-code-a-handbook-of-agile-software-craftsmanship-9780132350884)

本技能借鉴可读性、局部职责、明确边界、可验证行为与渐进改善的原则，
将其转为设计、实现、审查和重构时的工程判断。具体语言、现有契约与任务需求优先于书中的风格建议。
授权范围、证据分级、停止条件、跨语言裁决和并发检查流程属于面向 Agent 的适配，
不代表原书原句或作者逐项认可的规范。示例用于展示取舍，不是可直接部署的组件。

## 补充一手来源

1. **Robert C. Martin 与 John Ousterhout 的公开讨论**
   [A Philosophy of Software Design vs Clean Code](https://github.com/johnousterhout/aposd-vs-clean-code)
   使用其关于拆分粒度、理解成本与注释的讨论校准适用边界。双方承认过度拆分存在，但对理想粒度仍有分歧；不能据此声称作者放弃小函数。

2. **Go 官方：Effective Go**
   [文档](https://go.dev/doc/effective_go)
   用于稳定的 Go 错误返回、资源和语言惯用法。该文不持续覆盖新语言特性，不能作为具体最新 API 的依据。

3. **Go 官方：Code Review Comments**
   [文档](https://go.dev/wiki/CodeReviewComments)
   用于使用方接口、命名、空 slice 的外部表示等适配。

4. **Python：PEP 8**
   [文档](https://peps.python.org/pep-0008/)
   用于项目约定与兼容性优先、None 判断、异常范围和资源管理等适配。

5. **TypeScript 官方：Narrowing**
   [文档](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
   用于真假值边界、判别联合与 never 穷尽检查。

6. **Sandi Metz：The Wrong Abstraction**
   [原文](https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction)
   用于重新评估已经造成耦合的共用抽象。条件分支本身不是错误抽象的充分证据。

7. **Google Engineering Practices：What to look for in a code review**
   [文档](https://google.github.io/eng-practices/review/reviewer/looking-for.html)
   用于面向当前需求的设计审查、测试有效性及与变更风险匹配的验证层级。

补充材料用于工程裁决。日常使用本技能无需联网；
只有当前任务依赖具体版本、第三方行为或未知事实时，才补充查证。
