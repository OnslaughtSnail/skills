# 判断示例

## 目录

- 1. 改清楚语义，不增加层次
- 2. 简化表达式不能合并不同输入
- 3. 同一行为可以有多个断言
- 4. 合法分支不必改成继承
- 5. 缩小函数不能打破原子操作
- 6. 空集合与失败属于不同决策
- 7. 相同形状不一定代表同一政策
- 8. 没有必要改动时的合格结果

## 1. 改清楚语义，不增加层次

假设输入是已验证的记录序列，duration_ms 为整数毫秒，既有超时条件是严格大于预算：

~~~python
def expired_job_ids(records, budget_ms):
    return [
        record["id"]
        for record in records
        if record["duration_ms"] > budget_ms
    ]
~~~

函数已集中表达一个筛选行为。无需再拆出 get_duration、exceeds_budget、get_id 三个转发函数。
如果原代码只是变量叫 d、t，局部改名已经可能完成任务；无需新类、配置对象或策略接口。
不要顺便把 > 改成 >=，也不要擅自新增缺失字段默认值。

## 2. 简化表达式不能合并不同输入

假设 timeout 的契约是 None 使用默认值，0 表示不等待，正数为秒：

~~~python
def resolve_timeout(timeout, default_timeout):
    return default_timeout if timeout is None else timeout
~~~

改成 timeout or default_timeout 会改变 0 的含义。
这个函数作为示例用于暴露缺省规则；实际代码若只用一次且就地表达清晰，可以直接保留表达式。
不因示例有函数，就要求所有缺省逻辑都抽取。

## 3. 同一行为可以有多个断言

~~~python
def test_zero_timeout_is_preserved():
    assert resolve_timeout(0, 30) == 0
    assert resolve_timeout(None, 30) == 30
~~~

此处两个输入共同明确“缺失与零不同”的边界。可以按项目风格参数化，也可保留两个断言。
不需要为断言数量增加测试基类或模板方法。真正要确认的是错误的真值改写能被测试发现。

## 4. 合法分支不必改成继承

~~~typescript
type JobState =
  | { kind: "queued"; position: number }
  | { kind: "running"; worker: string }
  | { kind: "finished"; result: string };

function describeJob(state: JobState): string {
  switch (state.kind) {
    case "queued":
      return "queue:" + state.position;
    case "running":
      return "worker:" + state.worker;
    case "finished":
      return state.result;
  }
  const impossible: never = state;
  return impossible;
}
~~~

若这些状态构成受类型约束的封闭集合，这段分支清楚且能检查遗漏。
仅有 switch 不构成问题。运行时来自不可信外部的数据仍需在真实入口验证。
若未来多个模块重复同一分派，才根据实际修改成本重新评估边界。

## 5. 缩小函数不能打破原子操作

下面的简化示例约定：单进程内，一个 jobID 最多被首次认领一次。
claims 由外层状态持有者管理，mu 是所有访问遵守的同一把互斥锁：

~~~go
func claim(mu *sync.Mutex, claims map[string]string, jobID, workerID string) bool {
    mu.Lock()
    defer mu.Unlock()

    if _, exists := claims[jobID]; exists {
        return false
    }
    claims[jobID] = workerID
    return true
}
~~~

示例假设 map 已初始化；实际 API 应由状态拥有者封装 map 与锁，避免调用方配错。
不能为了命令／查询分离，改成分别加锁的 exists(jobID) 和 put(jobID, workerID)。
两次加锁之间可能被另一个调用插入。重构应保留一次完整的认领边界。

## 6. 空集合与失败属于不同决策

假设仓库契约要求查询失败返回 error；查询成功但没有对象返回 JSON 空数组：

~~~go
func listObjectNames(ctx context.Context, repo ObjectRepository) ([]string, error) {
    objects, err := repo.List(ctx)
    if err != nil {
        return nil, err
    }

    names := make([]string, 0, len(objects))
    for _, object := range objects {
        names = append(names, object.Name)
    }
    return names, nil
}
~~~

这里保留非 nil 的成功结果有对外表示依据。
不能把失败改成空数组加 nil error，也不能仅为“习惯写法”把成功空数组改成 nil。
ObjectRepository 仅代表现有依赖；不要求因为此示例而新增接口。

## 7. 相同形状不一定代表同一政策

两个接口都过滤记录并组装响应，但一个用于面向用户的列表，一个用于审计留存。
如果删除、权限、排序和字段保留规则会独立变化，不要创建带多个 mode 和布尔开关的通用处理器。
可以共享已确认相同的格式转换；政策继续在各自拥有者处表达。

反过来，若同一项单位转换或限额规则在多个位置各维护一份，应定位其权威并集中使用。
做出判断所需的是变化关系和不变量，不是“看起来有几行重复”。

## 8. 没有必要改动时的合格结果

审查一个职责集中、契约明确、没有确证风险的实现，可以报告：

> 当前范围内未发现需要修正的确证问题。现有分支与数据结构能直接表达需求，进一步拆分没有明确收益。已核对相关调用与测试；未覆盖部分按实际情况说明。

实际交付只能陈述真正完成的检查。不要复制这段话冒充验证记录。
