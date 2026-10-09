# 原神双语剧情资源

为[提瓦特 · 语旅](https://github.com/paintstar/Genshin-text-learning)维护中日双语剧情快照。资源更新独立于应用发布；客户端导入资源包后，可以离线搜索、阅读、查词和记录笔记。

本仓库已公开，Release 资源可以匿名下载。剧情来自 Project Amber，游戏文本及相关内容权利归原权利人所有；仓库公开状态不改变内容的权利归属。应用与资源包均不包含维护者的 GitHub 凭据。

## 本地采集

需要 Node.js 22.13 或更新版本。以下命令在仓库根目录执行：

```bash
node tools/story-data.mjs collect --cache-dir work/cache --output dist/story.gllpack --refresh-days 30
```

首次运行同步全部已发布的双语任务。后续运行优先获取新增任务和目录信息有变化的任务，并重新检查距上次成功检查超过 30 天的旧任务。已收录的历史任务不会因为上游目录移除而消失；目录数量异常减少时停止发布，保留旧资源。`--refresh-all` 强制检查全部正文；`--ids 1702,1703` 可以采集指定任务，用于小范围验证。目录的变化不能代替正文复查。

请求保持串行，开始时间间隔至少一秒，网络错误、5xx 和 429 有限重试并尊重 Retry-After。连续五次失败时暂停。每个任务的双语正文完成检查后共同保存，失败保留旧正文；首次缺失正文时不发布不完整资源。中断后重新执行即可续采。

采集会整理应用所需的目录、文本、对白节点及分支关系，不下载图片或语音。资源包为 `.gllpack`，采用 gzip 压缩的 JSON Lines：第一行包含格式版本、数据版本和双语目录，后续每行包含一个任务的配套双语正文及校验值。应用流式检查整个包后按任务事务导入，保留学习数据。格式版本与数据版本分别管理。

`build` 可使用已有缓存重新打包。`fixture` 只用于开发样本，资源包会明确标记为测试数据，正式应用构建会拒绝使用。

## 定期更新

`更新剧情资源` 工作流每周六北京时间 04:17 调度，也支持手动运行。默认分支必须保持工作流启用。GitHub 的调度可能延迟，公共仓库长期无活动时可能停用；维护时应检查最近一次成功运行及 Release。任务在 GitHub 独立运行，不依赖聊天或本机网络。

上一份 Release 的采集缓存用于恢复长期状态，Actions 缓存用于恢复尚未发布的断点。有内容变化才发布新版本，旧 Release 保留。采集报告仅保存为 Actions 附件。

每份 Release 包含 `story.gllpack`、`latest.json` 和维护端使用的 `source-cache.tar.gz`。`latest.json` 中的包地址指向固定版本；发布完成后 GitHub 的 latest 入口指向新版本。应用的默认清单地址为 `https://github.com/paintstar/Genshin-dataset/releases/latest/download/latest.json`，可以匿名访问；维护缓存不随安装包提供。

正式资源也可在应用源码中执行 `cargo run -p xtask -- story-pack inspect <文件路径>`，核对应用解析兼容性；携带资源的桌面构建会自动执行此检查。
