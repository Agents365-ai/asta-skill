# 使用方式

[English](USAGE.md)

直接用自然语言描述需求即可:

```
> 用 Asta 查一下 DOI 10.48550/arXiv.1706.03762 这篇论文

> 在 Asta 上搜索 2023 年以来 NeurIPS 的 mixture-of-experts 论文

> "Attention Is All You Need" 被哪些论文引用?按引用数排前 20

> 在 Asta 语料库中查找提到 "flash attention latency" 的段落

> 在 Asta 上找 Yann LeCun,列出他 2024 年的论文
```

技能会选对 Asta 工具、附上安全的 `fields` 参数,并遵循文档中的工作流模板。

## 示例：检索 + 批量下载（与 `paper-fetch` 联用）

`asta-skill` 只负责**检索和元数据获取**，不下载 PDF。如果要从搜索结果直接拿到本地 PDF，可与 `paper-fetch` 技能（或任意基于 DOI 的下载工具）串联使用：

```
> 用 Asta 检索 2022 年以来 "single-cell ATAC-seq batch correction" 引用最高的 5 篇论文，
  然后把 DOI 交给 paper-fetch 批量下载到 ./papers/ 目录
```

底层流程：

1. **asta-skill** → 调用 `search_papers_by_relevance`，参数 `publication_date_range="2022:"`，`fields=title,year,authors,venue,tldr,externalIds`（注意带上 `externalIds` 才能拿到 DOI）
2. Agent 从结果中提取 `externalIds.DOI`；没有 DOI 时回退到 `externalIds.ArXiv`
3. **paper-fetch** → 批量按 Unpaywall → arXiv → bioRxiv/medRxiv → PMC → SS → Sci-Hub 顺序解析每个 DOI/arXiv ID
4. PDF 落到 `./papers/`，每篇一个文件

`paper-fetch` 是独立技能，需要下载能力时单独安装。`asta-skill` 本身职责仅限 Semantic Scholar 语料。

**Step 1 — Asta 返回 Top 5 论文（含 DOI）：**

![Asta 检索结果](docs/images/asta-search.png)

**Step 2 — paper-fetch 把 5 篇 PDF 全部下载到 `./papers/`：**

![paper-fetch 批量下载](docs/images/asta-paper-fetch.png)
