# 五份参考模块独立审校记录

> 本文件保留独立审校阶段的问题与修订历史；最终结论及哈希见末尾复核表。文中“未提交或推送”等表述描述审校阶段。

审校日期：2026-10-02。审校者与译写者分离。本次逐一阅读五份原文的全部 484 行及对应正文译行，并阅读 116 个前置词表条目和各模块概念说明；另外使用自写纯文本检查，未执行仓库脚本或译文命令，未调用 GPU／模型推理，未修改译文、提交或推送。

## 审校结论与覆盖

正文全部覆盖，无摘要替代或整段漏译。可执行命令和控制结构共 54 个非注释行全部保持；所有数值、否定条件、表格行和链接指向逐项核对。发现一个占位标签原样保留问题、一个词表 IPA 问题，以及两组轻微术语／词性图例一致性建议。无 P0/P1 问题。

| 文件 | 源文范围 | 对应正文译行 | 逐行语义结论 |
|---|---|---|---|
| references/01-forensics-commands.md | 1–133，全文件 | .zh-CN.md 41–173 | 133/133 行完整；§1–14、全部命令及注释、音画核对矩阵、三项同时满足的误检条件完整。正文未发现翻译错误。 |
| references/02-h3-field-mapping.md | 1–76，全文件 | .zh-CN.md 44–119 | 76/76 行完整；十四字段表、时间戳规则、完整模板、三层声音、三种首尾帧模式、音乐字段清单完整。需恢复一处标签。 |
| references/03-edit-effects.md | 1–78，全文件 | .zh-CN.md 44–121 | 78/78 行完整；四层映射、定格四要素、两个长模板、漫画递进表、负面约束及末尾三行音效提示均完整。正文未发现漏译／误译；词表 IPA 有一项待修。 |
| references/audio-heuristics.md | 1–93，全文件 | .zh-CN.md 48–140 | 93/93 行完整；A1–A3 与 B、响度数值表、七行音乐模板、五行环境模板、六行动作声模板及全部禁止事项完整。未发现语义错误。 |
| references/ffmpeg-cheatsheet.md | 1–104，全文件 | .zh-CN.md 46–149 | 104/104 行完整；各抽帧／音频／差分／首尾帧命令与循环，八行故障表均完整。需改进 contact sheet 术语。 |

## 问题清单

P2：局部准确性／占位符保留问题，建议交付前修。P3：非阻塞文字或一致性建议。这里记录的是审校时快照，后续修订另记。

| ID | 等级 | 源位置 | 译位置 | 问题与建议 |
|---|---|---|---|---|
| REF-A01 | P2 | 02-h3-field-mapping.md:35 | 02-h3-field-mapping.zh-CN.md:78 | `<Subject N / off-camera actor>` 变成 `<Subject N / 画外演员>`。虽其中有自然语言，但译文前言承诺结构性素材标签原样保留，且用户要求标识符保持。建议完整保留源标签，标签外补“（画外行动者）”，正文仍中文；“行动者”也与其他模块统一。 |
| REF-A02 | P2 | 03-edit-effects.md:42、61 的 onomatopoeia | 03-edit-effects.zh-CN.md:24（词表） | `/ˌɒnəʊˌmætəˈpiːə/` 中的第二音节混入 /əʊ/。建议采用有词典依据的英式 `/ˌɒnəˌmætəˈpiːə/`。词性与释义正确。 |
| REF-A03 | P3 | 01-forensics-commands.md:22、92；ffmpeg-cheatsheet.md:50 | 01-forensics-commands.zh-CN.md:19、62、132；ffmpeg-cheatsheet.zh-CN.md:8、31、95 | contact sheet 在根目录是“缩略图总览表”，01 是“接触印样表”，ffmpeg 是“联系表”。“联系表”不自然且容易脱离图像语境，建议统一为“缩略图总览表”，首次可附“接触印样表”同义词。本意见涉及术语一致性，现有定义仍能说明其用途。 |
| REF-A04 | P3 | 新增前置词表，无源行 | 03-edit-effects.zh-CN.md:13、35；audio-heuristics.zh-CN.md:14、38；ffmpeg-cheatsheet.zh-CN.md:14、38 | 03 的词表使用 `v. phr.` 而图例未说明；audio 与 ffmpeg 使用 `prop. n.` 而图例未说明。建议补全缩写图例。 |

REF-A02 外部核验依据：[Collins onomatopoeia 的 British English 小节](https://www.collinsdictionary.com/dictionary/english/onomatopoeia)，提供 `/ˌɒnəˌmætəˈpiːə/`。其他词表逐项人工复核后未发现明确 IPA 错误；未逐词进行外部词典查询。不为工具、专名和缩写臆造 IPA 的策略已明确。

## 人工语义核验重点

- **01**：全部采样频率、窗口边界、阈值和路径正确保持；`> 0.8`、0.4/0.3、±0.1s、72.8BPM 未反向；三条齐备才判无稳定 BGM、亮度突变不能直接等于切镜、自由散板速度低置信度均保留。五行时间事件及全程一行矩阵没有丢失。命令内 burst、onset 仅在注释中翻译。
- **02**：必填与条件必填区别保留；首镜头无时间戳、时间戳严格递增、只标真实状态变化、禁止中性姿势复位未弱化。模板逐字段读过，保留 NEVER、NO、每次、只允许、三选一等范围；放大幅度／速度／动机、饮水链、头发服装延迟与无自主动画、全部负面约束、音乐静音段全部中文化。I2VA 的长锚定句完整翻译，`<Picture 1>` 与 `[Shot 1]` 保持。
- **03**：定格判据为同时满足、0.3–0.8s 与 0.5–0.6s 未混淆；仅一种强调色、图形仅一次弹出抖动、几帧内退出、特效不残留、不扩散到非指定节拍、实拍仅定格内卡通化等约束完整。SPLAT/COUGH/DON 为图形拟声示例，源文明确允许中文替换，因此“噗／咳咳／咚”属正确翻译，不误报为代码改写。末尾跨三行句子通读连贯。
- **audio**：先分类再音乐设计、三个条件必须全部成立、随机瞬态不当音乐、无音乐字段静音、每项响度正负号与不等号全部保留；音乐类型／配器／速度／力度描述无缩写式摘要。`<d>`、`[Language]`、`<type>`、`<Subject 1>` 完整；text 作为对白自然语言占位说明译为“文本”。所有禁止事项保持否定。
- **ffmpeg**：PowerShell 续行反引号、变量、循环上下界、格式字符串、转义反斜杠、filter 字符串、路径、命令开关逐行核对；所有可运行代码仅行尾／独立注释自然语言变化。按片长选间隔、“减半间隔补抽”、输入／输出定位区别和缺音轨时跳过分析保持。

## 原文技术问题／不一致（非翻译错误）

- 01-forensics-commands.md:3 引用 §12 作为中文路径处理说明，但 §12 实为响度补充判据；译文第43行忠实保留该引用，不擅改。
- 02-h3-field-mapping.md:74 称音乐“十项写法”，第76行实际列出九项；译文保留源文数量表述和所有九项，不臆造第十项。
- audio-heuristics.md:31 把不满足稳定 BPM 条件一概判作只有现场声，与其他文档关于自由散板可能仍是音乐的说明有张力；译文忠实保留，不在翻译时重写算法结论。
- audio-heuristics.md:86 要求所有声音细节放入 overall_soundscape，而 02-h3-field-mapping.md:64 又要求动作同期声内嵌 integrated_multimodal_description。两处译文均忠实各自原文。
- ffmpeg-cheatsheet.md:84 用“明亮差分＝硬切”的简化判据，与 01 的火光伪切排除需合读；译文没有将其改成新规则。首尾帧命令、`-noaccurate_seek` 建议等保持源样，本次未执行或声称验证这些操作的运行结果。

## 纯文本辅助证据

五份正文均与源文行数相等、空行掩码一致；逐源行数字集合无丢失；PowerShell/Bash 块去除注释后的 54 个非空行全部相同（01 为17行、audio 为5行、ffmpeg 为32行，含循环结构）。源文固定字段与路径未丢失。尖括号扫描发现 REF-A01；另将 ffmpeg 的 `<5s…>15s` 比较表达式误识别为标签，人工排除该假阳性。Markdown 链接及所有真实路径引用保持原目标；01/02/03 的参考路径使用原文反引号形式。

| 文件 | 源文 SHA-256 | 译文审校快照 SHA-256 |
|---|---|---|
| 01-forensics-commands | `42848e2c56e2e895d5fc3e0f01661ea9ae635d0cb243ccb0abf114079fe786a1` | `0fad30fd12050d4fa0ed7abd3deab44571b1dcf2f01347a812c47ab21aa48c55` |
| 02-h3-field-mapping | `90fae7760610001ff6bfa6cec3ffd0c533eb99e8b0d84f603dc318aac3a898d4` | `f61bb2b7fadaa9d26eae81be88f0fa1091640c23183b746dd42723c0d5af6142` |
| 03-edit-effects | `3f626e064bca104d40def0ea4daaa7766caa308493b62e288ba24ae8b3f4de66` | `a272b1019a5eb6400eddf1ac06ea096dd0e699346c7dd429a0324242f82e2bbf` |
| audio-heuristics | `1682a13a8a7dd6e5927f0a4eef6705e8ba727b18f35cd00559bc05639b73494d` | `78cef345b637a77fe8190b2d4340a395935a70bed3da08b3531b7515ccb0da0f` |
| ffmpeg-cheatsheet | `1288d027ac96ac5878cd2071fcbc2129edfeb89bcca683216b6dfe3e3b7c63e4` | `39fe12e3bffdaad8e24d8a086ce30ec27b6c1affd7985a5dcf94a9928dcb6377` |

## 最终修订复核（已关闭全部审校问题）

主译者停止修改后，独立复核 REF-A01 至 REF-A04：完整 `<Subject N / off-camera actor>` 已恢复，标签外另给中文释义；onomatopoeia IPA 已改；contact sheet 已统一“缩略图总览表”（01保留接触印样表同义说明）；三份词性图例已补齐。字段标签在词表中加反引号以确保可见，叙事外音乐术语统一，未改变正文语义。

重新核对这五份全部源行对应关系、空行位置、参考标签和链接；54个命令／循环非注释行仍与源文完全相同。所有本次问题已关闭，无未处理翻译问题。首次行号与快照仍保留，稳定定位优先采用源行号。

| 最终译本 | 正文行数 | 当前正文译行范围 | 最终复核 SHA-256 |
|---|---|---|---|
| references/01-forensics-commands.zh-CN.md | 133 | 41–173 | `0ffe907768beb8a430c7c6e5af045f529de755bf98a4dd0f08e21d51dc1ebf07` |
| references/02-h3-field-mapping.zh-CN.md | 76 | 44–119 | `a23cea2758ff6223ccc6f474decc54ec7fe0b91b0cbeef698210756056241a49` |
| references/03-edit-effects.zh-CN.md | 78 | 44–121 | `7e363524d99ca85e8bfd4876a61eacd9ccf717c2a5d0c452fa520ec264e48a81` |
| references/audio-heuristics.zh-CN.md | 93 | 48–140 | `8562292a11c09ed9bfcd9523141c48629b8c68c8c984630b0e3aa5c6698b12b5` |
| references/ffmpeg-cheatsheet.zh-CN.md | 104 | 46–149 | `d3e945d3f92b97a7d13aa3edffc79c1918483b8c6a41c359b6a65e10357e5190` |
