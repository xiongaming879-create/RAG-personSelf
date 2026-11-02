# 企业知识库 RAG 问答系统

针对公司年度报告 PDF 进行问答，核心组合拳包括：

- **Docling 自定义 PDF 解析**（表格序列化处理）
- **父文档检索**（Parent Document Retrieval）的向量搜索
- **LLM 重排序**（Reranking）提升上下文相关性
- **结构化输出提示词** + 思维链（Chain-of-Thought）推理
- **查询路由**，支持多公司对比类问题

## 免责声明

上手前先看几点：

- IBM Watson 集成跑不通
- 代码有毛边和奇怪的 workaround
- 没有测试，错误处理很少，踩坑自负
- 需要自备 API Key（OpenAI / Gemini，重排序用 Jina 可选）
- GPU 对 PDF 解析加速明显

## 快速开始

```bash
git clone <你的仓库地址>
cd RAG-personSelf
python -m venv venv
venv\Scripts\Activate.ps1  # Windows (PowerShell)
pip install -e . -r requirements.txt
```

复制 `.env.example` 为 `.env`（或直接编辑 `.env`），填入自己的 API Key：

```ini
OPENAI_API_KEY=sk-...     # 必填
GEMINI_API_KEY=AIza...    # 可选
JINA_API_KEY=jina_...     # 可选，LLM 重排序用
```

## 测试数据集

仓库包含两套数据：

1. **小型测试集**（`data/test_set/`）：5 份年报 PDF + 问题，适合快速跑通流程
2. **完整 ERC2 数据集**（`data/erc2_set/`）：全部问题和报告

每套数据各自带 README 说明，可以用来：

- 学习示例问题、报告与系统输出
- 用 PDF 从零跑完整流程
- 用预处理好的数据跳到任意流水线阶段（解压对应 zip 即可）

详见：
- `data/test_set/README.md` - 小型测试集说明
- `data/erc2_set/README.md` - 完整数据集说明

## 使用方法

### 方式一：运行流水线

在 `src/pipeline.py` 里取消注释想跑的阶段，然后：

```bash
python .\src\pipeline.py
```

### 方式二：CLI 命令

在数据目录下运行 `main.py`：

```bash
cd .\data\test_set\
python ..\..\main.py process-questions --config max_nst_o3m
```

查看所有命令：

```bash
python main.py --help
```

可用命令：

| 命令 | 作用 |
|---|---|
| `download-models` | 下载 Docling 所需模型 |
| `parse-pdfs` | 解析 PDF 年报（支持并行/串行，`--chunk-size`、`--max-workers` 等参数） |
| `serialize-tables` | 处理解析结果中的表格 |
| `process-reports` | 对解析好的报告跑完整流水线 |
| `process-questions` | 按指定配置处理问题 |

示例：

```bash
python main.py parse-pdfs --help
# 查看并行/串行、--chunk-size、--max-workers 等选项

python main.py process-reports --config ser_tab
# 以序列化表格配置处理报告
```

## 常用配置

- `max_nst_o3m` - 最佳配置，使用 OpenAI o3-mini
- `ibm_llama70b` - 使用 IBM Llama 70B（跑不通可无视）
- `gemini_thinking` - 利用 Gemini 超大上下文窗口全量回答（严格说这不是 RAG）

更多配置见 `src/pipeline.py`。


## License

MIT
=======
# RAG-personSelf
个人rag知识库

