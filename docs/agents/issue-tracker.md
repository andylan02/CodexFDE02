# 问题跟踪：GitHub

这个仓库的事项与需求默认以 GitHub Issues 形式管理。工程类技能应该优先使用 `gh` CLI 进行创建、查询和更新。

## 约定

- 创建问题：`gh issue create --title "..." --body "..."`
- 查看问题：`gh issue view <number> --comments`
- 列出未关闭问题：`gh issue list --state open --json number,title,body,labels --jq '[.[] | {number, title, body, labels: [.labels[].name]}]'`
- 评论问题：`gh issue comment <number> --body "..."`
- 添加/移除标签：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- 关闭问题：`gh issue close <number> --comment "..."`

当前仓库远程地址为 `https://github.com/andylan02/CodexFDE02.git`，因此 GitHub 是工程技能默认采用的事项来源。

## PR 是否作为 triage 入口

PR 作为需求入口：否。

这个仓库目前不将外部拉取请求作为首选的 triage 入口。除非后续工作流显式开启 PR 驱动的需求流，否则工程技能应优先处理 GitHub Issues。

## 当技能要求“发布到 issue tracker”时

在当前仓库中创建一个 GitHub issue。

## 当技能要求“获取相关 ticket”时

使用 `gh issue view <number> --comments` 以及相关 issue 信息进行定位和阅读。

## 领域说明

这个仓库当前使用单上下文默认布局。根目录中尚未出现专门的领域术语文件，因此工程技能在未新增领域文档前应避免假设有定制词汇表。
