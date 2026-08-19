# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

质量审核明确拒绝一条漂移事件后，审核记录已保存，但数据快照仍处于隔离状态，后续就绪检查持续阻塞。请修复拒绝结论的跨实体状态更新。 请只修改必要的生产代码，不得新增、删除或修改测试文件，不得跳过测试或放宽断言。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/ai-featuremesh-task-14
- 仓库地址：https://github.com/zhanglei10281852-gif/ai-featuremesh-task-14.git
- parent SHA：f994efd9d90cfd81cc1bd23901c447826c54426d

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/ai-featuremesh-task-14.git bug-repro
cd bug-repro
git checkout --detach f994efd9d90cfd81cc1bd23901c447826c54426d
go test ./internal/service -run "^TestRejectedReviewRejectsSnapshots$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestRejectedReviewRejectsSnapshots$" -count=1
FAIL	github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service [build failed]
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
# github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service [github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service.test]
internal/service/review.go:78:12: undefined: excursion

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestRejectedReviewRejectsSnapshots$" -count=1
FAIL	github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service [build failed]
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
# github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service [github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service.test]
internal/service/review.go:78:12: undefined: excursion

```

## 通过条件

定向公开行为验证通过，相关包和全量测试通过，go vet 及 linux/amd64 构建通过。 定向命令必须由修复前失败变为修复后通过；相关包、go test ./... -count=1、go vet ./... 和 linux/amd64 构建必须通过；回退 gold 关键修改后定向命令重新失败。
