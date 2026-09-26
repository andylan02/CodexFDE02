# Copilot 指南：CodexFDE02

这是一个围绕“个人研发工作台 + 课程交付 + FlowERP 客户项目”三层关系设计的仓库，不是通用应用。核心理念是：在本仓库构建工作台/课程 Harness，并通过它管理对独立 FlowERP 项目的受控交付、评审和验证。必须时刻保持这条边界清晰。

## 环境与启动

使用仓库本地虚拟环境，并优先使用项目内的 Python 调用方式：

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -e .
```

macOS / Linux：

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -e .
```

常见启动命令：

```powershell
python main.py
python -X utf8 -m workbench.cli serve-workbench
python -X utf8 -m workbench.cli serve
python -X utf8 -m workbench.cli environment-check
```

这个仓库的重要规则：

- 使用 `.venv` 和项目解释器，不要默认使用系统 Python。
- `.runtime` 与 FlowERP 运行时必须分开，不能混用工作台数据库和客户项目数据库。
- 工作台入口通常是 `http://127.0.0.1:8001/`，FlowERP 入口是 `http://127.0.0.1:8000/`。
- 本仓库聚焦编排、证据链、课程工作流；不要把 ERP 业务逻辑当作本仓库一部分来导入。

## 构建、测试与校验命令

`pyproject.toml` 中没有单独的 lint 目标；这个项目的质量信号主要来自 Python 单元测试和阻断级 Eval harness。

运行完整测试集：

```bash
python -X utf8 -m unittest discover -s tests -v
```

运行单个测试文件或单个测试用例：

```bash
python -X utf8 -m unittest tests.test_workbench -v
python -X utf8 -m unittest tests.test_workbench.TestWorkbenchCase.test_something -v
```

运行浏览器状态测试（`.test.cjs`）：

```bash
node --test tests/*.test.cjs
```

运行 CI 使用的阻断级 Eval：

```bash
python -X utf8 -m eval.harness --suite blocking
```

运行完整 Eval 集合（blocking + observing）：

```bash
python -X utf8 -m eval.harness --suite all
```

其他仓库原生的关键检查命令：

```bash
python -X utf8 -m workbench.cli environment-check
python -X utf8 -m workbench.cli demo
python -X utf8 -m agent.loop --max-rounds 3
python -X utf8 -m agent.graph --max-rounds 3
python -X utf8 -m workbench.feedback summary
python -X utf8 -m workbench.cli course-status
```

单节课程检查：

```bash
python -X utf8 -m workbench.cli course-contract --lesson 4
python -X utf8 -m workbench.cli course-eval --lesson 4
```

## 高层架构

这个仓库的结构更像“交付工作台”而不是传统单应用：

- `workbench/`：CLI、项目注册、Spec、执行管理、证据、运行时配置等。
- `eval/`：质量门禁与评估中心；是阻断性检查的权威入口，结果输出到 `.runtime/reports/`。
- `agent/`：任务循环、状态图、可恢复执行逻辑，和课程进度相关。
- `workbench_web/`：工作台最小驾驶舱，默认 8001。
- `harness_web/`：可选完整 Harness 平台，不是课程通过条件。
- `main.py`：启动本地工作台，并可能按保存的运行时配置启动外部 FlowERP 服务。

关键边界：FlowERP 被故意放在这个仓库之外。本仓库负责编排外部项目、记录决策、保留证据，并在正确上下文中运行验证，而不是把 ERP 业务代码直接导入到这里。

## 代码库中的关键约定

- 把它当作交付平台，而不是一堆零散脚本：需求、Spec、执行、评审和证据必须保持关联。
- 工作台状态与 ERP 产品状态必须分开；不要混用 `.runtime`、数据库或运行目录。
- 优先使用最小且贴合改动的校验命令；如果是面向用户或交付关键的修复，优先走 Eval harness，而不是临时手工脚本。
- 保留失败证据，不要把失败伪装成成功。
- 与外部项目协作时，优先使用已注册的项目根目录或 `FLOWERP_PROJECT_ROOT`，不要硬编码随机路径。
- 写入范围应尽量收窄，必要时使用隔离候选补丁/工作区。
- `AGENTS.md`、`README.md` 以及课程契约属于运行时约束；影响课程流转或交付边界时应遵守。

## 修改前建议先看这些文件

- `README.md`：仓库目标和边界说明
- `AGENTS.md`：课程治理和运行约束
- `pyproject.toml`：安装与测试入口
- `workbench/cli.py`：命令面板和工作台入口
- `eval/harness.py`：阻断级评估语义
- `workbench/project_registration.py`：外部仓库注册和安全规则

## 默认工作方式

1. 先确认改动属于工作台、独立 FlowERP 项目，还是可选 Harness UI。
2. 优先使用本地项目环境和最小相关校验命令。
3. 保持证据链完整，尤其在任务状态、评审结论或补丁选择发生变化时。
4. 不要因为工作台检查通过，就直接认为外部 ERP 项目已验证成功；必须运行对应的项目级验证。

## 说明

- 本文件是给未来 Copilot 会话的仓库级指导信息。
- 后续所有文档、说明和代码注释应保持中文表述，避免混用中英文，以便整个项目更一致地面向中文协作。
