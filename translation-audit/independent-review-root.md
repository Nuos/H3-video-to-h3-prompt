# 根目录文档独立审校记录

> 本文件保留独立审校阶段的问题与修订历史；最终结论及哈希见末尾复核表。文中“未提交或推送”等表述描述审校阶段。

审校日期：2026-10-02。范围：README.md 与 SKILL.md 及各自 `.zh-CN.md`。审校方式：逐一阅读全部源行与对应译行，复核前置概念与词表；另用自写纯文本检查核对空行位置、数值、反引号标识符和链接。未运行仓库脚本，未修改译文，未提交或推送。

## 结论

两文件共 209 源行全部逐行审读，正文无漏段、摘要替代、条件反转或 CLI／字段改写。发现 2 项需修的词表准确性问题和 3 项轻微文字／格式问题。下列行号与 SHA-256 对应审校时快照；主译者后续修改需另记复核状态。

| 文件 | 源文检查范围 | 对应正文译行 | 完整性与语义检查 |
|---|---|---|---|
| README.md | 1–87，含目录树全部注释、Windows 与 Bash 命令、触发语句和许可证链接 | README.zh-CN.md 44–130 | 87/87 行；全部空行对应；八项功能、六项检查、逆问题说明和交付条件完整 |
| SKILL.md | 1–122，含 YAML 描述、十步流程、五模式表、提示语及资源列表 | SKILL.zh-CN.md 49–170 | 122/122 行；全部空行对应；原有中文未删；英文自然语言均有完整中文表达或紧邻中文释义 |

人工重点复核：4–8fps、≥8fps、3–5 帧、0.5s、117 BPM、15s 分段阈值及比较方向；“只有明确要求才存盘”、无参考图／有参考图条件、A/B/C 片型互斥、禁止慢动作／漂移、音乐不中断、不可套 LOCK/BURST、只移动相机不变形人物等限制均保留。音乐／同期声／环境音字段分工、参考标签、CLI 参数、路径、五种模式及十四字段／六段式区别保持。术语 onset、roll、hand-off、state machine 译法在两文件间一致。

## 待处理问题

等级：P2 为局部准确性问题，建议交付前修；P3 为轻微文字或格式建议。无 P0/P1。

| ID | 等级 | 源位置 | 译位置 | 问题及建议 |
|---|---|---|---|---|
| ROOT-01 | P2 | README.md:9，rubato | README.zh-CN.md:26（前置词表） | IPA 写成 `/rʊˈbɑːtəʊ/`。Cambridge 和 Collins 的英式词条均给长元音 `/ruːˈbɑːtəʊ/`，建议更正；正文语义正确。 |
| ROOT-02 | P2 | SKILL.md:48，“原片无 BGM”语境 | SKILL.zh-CN.md:28（前置词表） | explicit silence 释义“明确声明没有音乐或声音”可能被理解为清除同期声；本模块要求无配乐时明确写无配乐。建议“明确声明无背景音乐；本语境不要求消除同期声”。正文第96行忠实，但可将“明确写静音”补明为“明确写音乐静音”以免歧义。 |
| ROOT-03 | P3 | SKILL.md:34 的 trigger 概念 | SKILL.zh-CN.md:23（前置词表） | “引发动作用”末尾多“用”；改为“引发动作”。 |
| ROOT-04 | P3 | 新增词表说明，无源行 | README.zh-CN.md:9、22 | 第22行使用 `adj. phr.`，第9行词性缩写图例未解释该项；补“adj. phr. 形容词短语”。 |
| ROOT-05 | P3 | SKILL.md:33、64、67、75、107 | SKILL.zh-CN.md:81、112、115、123、155 | 英文替换后遗留“强音头 必须”“强音头 都”“强音头 区间”“相机滚转 纪律”“相机滚转 还是”等中文词间空格；建议清除。 |

ROOT-01 依据：[Cambridge rubato pronunciation](https://dictionary.cambridge.org/us/pronunciation/english/rubato)、[Collins rubato（含 British English 小节）](https://www.collinsdictionary.com/us/dictionary/english/rubato)。forensic 的 /s/ 与 /z/ 存在词典变体，已对照 [Collins forensic](https://www.collinsdictionary.com/dictionary/english/forensic)，不把现有 /s/ 误报为错误。montage 重音亦有英式变体，未强制统一成单一读法。其余词性、释义及 IPA 已逐条人工复核，未发现明确错误；未对所有词条逐条查询外部词典。

## 原文自身问题与保留处理

- SKILL.md:47 写“两位小数”却示例为 `00:03.200`（三位小数）。译文第95行忠实保留，译文第44行已有明确译注；不能以翻译名义擅自改规范。
- README.md:19 的 “only then” 与 SKILL.md:22 的“否→才”表述不完全相同。两译本分别忠实各自源文，不擅自统一流程条件。
- 源文资源速记 `references/06`、`references/07`、`references/08` 与根目录中 `ffmpeg-cheatsheet.md` 等是原有行文指向；译本保持。真正 Markdown 链接 `LICENSE` 及译本新增来源链接均存在。

## 纯文本辅助检查证据

两份正文的所有源反引号片段均存在于对应译行，无丢失；空行掩码一致；README 的 Markdown 链接目标 LICENSE 一致且文件存在。数字检查唯一表面差异为 README.md:40 的 `4-tuple` → README.zh-CN.md:83 的“四要素”，属正确文字化，并非数量丢失。两条可运行命令逐字相同；目录树仅自然语言注释译为中文。

| 文件 | 审校快照 SHA-256 |
|---|---|
| README.md | `6b4a254b66b2e1bff07cf3e9af3e5183f525f712e40fa9aef762e6b316d481ef` |
| README.zh-CN.md | `700fadcdddfd64ebf9dd6229edb7cb9ee256d95275457402d3f3d231744995f2` |
| SKILL.md | `5193e9156b91f82be219f8d5fae85be8a086a62b14522730481e5ca4ef436dec` |
| SKILL.zh-CN.md | `c8bce70fe20a339a9cddf6160d637bdf649e3f3b0182d6a873b07c8cf6fa0f3e` |

## 主译者修订后的独立复核

已重新读取修订内容：ROOT-01（rubato IPA）、ROOT-02（explicit silence 词表语境）、ROOT-03（trigger 笔误）、ROOT-04（adj. phr. 图例）均已修正。ROOT-02 正文原译可结合词表准确理解，不再列为准确性待办。ROOT-05 中文词间空格仍在，属于非阻塞排版建议。

复核时 README.zh-CN.md SHA-256：`2faa623d06d08e9751ba55721da7fbbc65a6e282a10668ce39a9a94d3222b1a5`；SKILL.zh-CN.md SHA-256：`f3de679904e37338995fc5b7b02d4f029caa143d5865fa1b587dacb9dcf7233d`。正文源行对应范围未变。

## 最终修订复核（已关闭全部审校问题）

主译者停止修改后，独立复核全部已报项：ROOT-01 至 ROOT-05 均已解决。README 新增中文导航位于正文标记之前，正文仍为87源行逐行完整对应；当前正文行号见下表。SKILL 中文词间空格已清除，术语统一没有改变原意。首次审校行号和哈希保留为历史依据，稳定定位优先采用源行号。

| 最终译本 | 正文行数 | 当前正文译行范围 | 最终复核 SHA-256 |
|---|---|---|---|
| README.zh-CN.md | 87 | 52–138 | `20767e572ad8aa6d2799c1cc7e89cc5094bef29629db25dd62c2fc731d8746b2` |
| SKILL.zh-CN.md | 122 | 49–170 | `4b89eb2f1a71ca5b3e056321cc851ef612b6a62c392c6638a6bb1590e52a1687` |

已重新检查固定参考标签、全部 Markdown 链接目标、空行对应及命令正文；没有新增问题。此范围无未处理翻译问题；原文矛盾继续按本记录保留说明。
