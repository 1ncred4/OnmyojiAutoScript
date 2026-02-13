# CLAUDE.md

此文件为 Claude Code (claude.ai/code) 在处理本仓库代码时提供指南。

## 项目概览

OnmyojiAutoScript (OAS) 是一个基于 Python 3.10+ 构建的《阴阳师》手游自动化工具。它与 AzurLaneAutoScript (Alas) 共享架构。系统包含一个 Python 后端，通过 ADB/UiAutomator2 处理游戏自动化逻辑，并为独立的 GUI 前端 (OASX) 提供本地服务器接口。

## 开发命令

### 环境设置
- **安装依赖**: `pip install -r requirements.txt`
- **生成依赖文件**: `pip-compile --annotation-style=line --output-file=requirements.txt requirements-in.txt`

### 运行应用
- **启动后端服务器**: `python server.py` (启动 FastAPI/Uvicorn 服务器)
- **运行自动化循环**: `python script.py` (直接运行主调度循环)
- **启动 GUI 服务**: `python gui.py`

### 辅助工具
- **资源提取**: `python dev_tools/assets_extract.py`
- **资源测试**: `python dev_tools/assets_test.py`

## 代码架构

### 目录结构
- `tasks/`: 包含具体的游戏任务实现。每个任务（例如 `Exploration`, `Hunt`）都是一个包含 `script_task.py` 的 Python 包。
- `module/`: 核心框架组件。
  - `module/config/`: 使用 Pydantic 进行配置管理。
  - `module/device/`: Android 设备控制 (ADB, minitouch, uiautomator2)。
  - `module/ocr/`: OCR 实现 (ppocr-onnx)。
  - `module/ui/`: UI 元素定义和交互。
- `config/`: 用户配置文件 (JSON)。
- `assets/`: 游戏资源（图片，模板匹配目标）。

### 核心概念
- **调度器 (`script.py`)**: 管理执行循环、任务调度和错误处理。
- **配置**: 使用定义在 `module/config/` 中的 `pydantic` 模型。配置会动态重新加载。
- **设备**: 封装 ADB/UiAutomator2 操作。处理截图和点击执行。
- **任务**: 继承自 `ScriptTask`。逻辑通常涉及：检查当前状态（截图） -> 决策 -> 动作（点击/滑动）。

### 常用模式
- **任务实现**: 要添加新的游戏功能，请在 `tasks/` 中创建一个新目录，继承 `ScriptTask`，并实现 `run` 方法。
- **图像匹配**: 使用模板匹配或 OCR。资源在代码中引用并存储在 `assets/` 中。
- **日志**: 使用 `module.logger`。
- **RPC**: `zerorpc` 用于脚本进程与 GUI/服务器之间的通信。

## 指南
- **路径**: 文件操作请使用绝对路径或 `pathlib`。
- **类型提示**: 鼓励广泛使用 Python 类型提示。
- **错误处理**: 自定义异常定义在 `module/exception.py` 中。使用它们来控制流程（例如 `TaskEnd`, `GameStuckError`）。
