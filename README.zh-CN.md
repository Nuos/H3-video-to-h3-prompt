# 项目概览：简体中文译本

来源：[README.md](README.md)；版本：`7371c753ff4a087ca411e12033a3314231bd28c8`。原文完整保留。

## 概念、术语与重要单词释义

本模块介绍如何从视频证据重建可执行提示词：先取证与验证，再选择片型、镜头机制及 H3 模式。取证结论、实际拍摄动作、相机运动和后期效果须分层描述。下文是文档的完整中文译文；原文关于输出英文提示词的要求也忠实保留。

词性：n. 名词；v. 动词；adj. 形容词；adv. 副词；n. phr. 名词短语；v. phr. 动词短语；adj. phr. 形容词短语；abbr. 缩写；prop. n. 专名。IPA 采用常见英式读音，复合术语按组成词标注；专名、缩写和标识符没有确定的统一读音时不编造 IPA。

| 英文 | IPA | 词性 | 本模块语境含义 |
|---|---|---|---|
| reverse-engineer | /ˌrɪˈvɜːs ˌendʒɪˈnɪə/ | v. | 逆向推导，从成片还原生成描述 |
| forensic | /fəˈrensɪk/ | adj. | 以可核验证据为基础的、取证式的 |
| prompt | /prɒmpt/ | n. | 给生成模型的提示词 |
| dense | /dens/ | adj. | 密集的，指高频率抽帧 |
| frame | /freɪm/ | n. | 视频的一帧画面 |
| contact sheet | /ˈkɒntækt ʃiːt/ | n. phr. | 多帧拼成的缩略图总览表 |
| hypothesis | /haɪˈpɒθəsɪs/ | n. | 待证据检验的剧情或机位假设 |
| triage | /ˈtriːɑːʒ/ | n., v. | 按片型分类分流，以选择相机机制 |
| state machine | /steɪt məˈʃiːn/ | n. phr. | 规定相机状态及状态切换的机制 |
| mutually exclusive | /ˈmjuːtʃuəli ɪkˈskluːsɪv/ | adj. phr. | 互斥的，不能同时发生 |
| montage | /mɒnˈtɑːʒ/ | n. | 通过多个镜头剪接构成的蒙太奇 |
| onset | /ˈɒnset/ | n. | 音头，声音事件的起始瞬间 |
| accent | /ˈæksent/ | n. | 音乐重音 |
| rubato | /ruːˈbɑːtəʊ/ | n. | 速度可自由伸缩的演奏处理；此处指自由节奏 |
| causal | /ˈkɔːzəl/ | adj. | 因果关系上的 |
| trigger | /ˈtrɪɡə/ | n., v. | 触发因素；触发动作 |
| reaction | /riˈækʃən/ | n. | 前一动作造成的反应 |
| overlay | /ˈəʊvəleɪ/ | n. | 叠加在画面上的图层 |
| segmentation | /ˌseɡmenˈteɪʃən/ | n. | 把长片划为多个生成段 |
| hand-off | /ˈhænd ɒf/ | n. | 分段交接，上一段末状态接续到下一段 |
| roll | /rəʊl/ | n., v. | 相机绕光轴旋转；滚转 |
| cosplay | /ˈkɒzpleɪ/ | n. | 以服装和造型扮演角色 |
| MiniMax H3 | 不编 IPA：产品专名，未规定统一发音 | prop. n. | 本项目面向的视频生成产品/模型 |
| T2VA / I2VA / FL2VA / L2VA / Ref2VA | 不编 IPA：模式缩写，保留原样 | abbr. | 分别为文本、首帧、首尾帧、尾帧、全参考输入模式 |
| RMS / BPM / RGB / SFX / SOP | 不编 IPA：缩写，按字母名称读 | abbr. | 均方根／每分钟拍数／红绿蓝／音效／标准操作流程 |
| `HARD LOCK` / `INSTANT BURST` | 不编整串 IPA：状态标签 | 标识符 | 完全锁定／瞬时爆发两个互斥相机状态 |
| `editing` / `overall_soundscape` / `non_diegetic_music` | 不编 IPA：字段标识符 | 标识符 | 剪辑／整体声景／叙事外音乐字段，拼写须保持 |

## 中文模块导航

- [工作流](SKILL.zh-CN.md)
- [01 取证命令库](references/01-forensics-commands.zh-CN.md) · [02 H3 字段映射](references/02-h3-field-mapping.zh-CN.md) · [03 剪辑特效](references/03-edit-effects.zh-CN.md)
- [04 角色替换](references/04-coser-replacement.zh-CN.md) · [05 陷阱与清单](references/05-pitfalls-checklist.zh-CN.md) · [06 片型与状态机](references/06-film-type-and-state-machine.zh-CN.md)
- [07 音头与卡点](references/07-onset-and-card-points.zh-CN.md) · [08 多图与分段](references/08-multi-image-and-segmentation.zh-CN.md)
- [音频启发式](references/audio-heuristics.zh-CN.md) · [FFmpeg 速查](references/ffmpeg-cheatsheet.zh-CN.md) · [镜头语法](references/shot-syntax.zh-CN.md)

## 完整正文译文

<!-- translation-body:start -->
# video-to-h3-prompt

通过取证式逆向分析，把参考视频转为完整、可直接粘贴的 **MiniMax H3** 提示词。这不是“看几帧就写一段氛围描述”，而是一套证据处理流程：重建因果事件链、按片型选择正确的相机状态机、对照音乐音头核验切镜、分离现场动作／相机／剪辑特效／声音各层，并为每一种输入模式准确组织 H3 字段。

## 功能

- **密帧取证**——先用稀疏采样的缩略图总览表建立骨架；对有歧义的时间窗口以 4–8fps 密集抽帧，在相互竞争的剧情／相机假设之间作出判断；最后用全分辨率关键帧和烧录时间码的图表确认。
- **片型 → 相机状态机分流**——把每条片段归为 (A) 连续长镜头、(B) 编辑式快照锁定（`HARD LOCK ↔ INSTANT BURST`，即完全锁定与瞬时爆发两个互斥状态），或 (C) 多镜头蒙太奇（命名机位 + 切镜纪律），从而避免用锁定／爆发模板压制真实切镜，或让静态主体展示画面发生漂移。
- **音乐音头／卡点同步**——除 0.5s RMS 包络外，`onset_probe.py` 还检测分级音头（只需 numpy+scipy，无需 librosa），把每次切镜／定格／冲击／齐射／气场爆发与强重音核对；区分固定速度与自由伸缩的节奏（不捏造 BPM），并过滤枪口火光／爆炸造成的伪切镜。
- **因果事件链重建**——每次状态变化都写明触发 → 动作 → 反应；将画外行动者的线索（一截袖子、一只手、一块踏板车导流罩）作为真实行动主体跟踪，而不是当作杂物忽略。
- **剪辑层分离**——定格笑料、白闪、漫画叠加、RGB 故障和能量爆发效果及其后期音效归入 `editing` / `overall_soundscape`，绝不能误当成拍摄现场动作。
- **多图锁定与长片分段**——`<Picture 1>` 锁定角色 1，`<Picture 2>` 锁定角色 2（只锁身份和服装；环境／道具仍用文字描述，不写任何外貌正文）；超过约 15s 的片段拆为多个生成段，每段独立计时，音乐不重新起奏，并以交接状态衔接。
- **全部五种 H3 模式**——T2VA / I2VA / FL2VA / L2VA 使用 14 字段模板；Ref2VA 使用六段式模板，并遵守 `<Subject>/<Picture>/<Video>/<Audio>` 标签纪律。
- **不改剧情的角色扮演者／角色替换**——只替换外观，检查动作兼容性，按层级决定道具是否出现，将动漫设计转译为真人角色扮演造型，并输出替换对照表。

## 六项可复用检查

1. 先判断是连续运镜还是离散切镜；以 ≥8fps 对转折段密集采样，寻找“运动 → 完全静止”的台阶变化。
2. 将时间轴与音频核对：每次切镜／定格／冲击是否落在音头上？是 → 按编辑式／卡点剪辑处理；之后才考虑自由安排的相机时序。
3. 有参考图时，绝不描述外貌——图像锁定外观；文字只承载相机／时序／状态机／环境与道具。
4. 把静态与爆发写成两个互斥状态；在各静态段反复明确“禁止慢动作／漂移／残余运动”。
5. 画面横置时，先怀疑相机滚转——比较地平线与身体的关系，再判断人物是否躺下／转身。
6. 冲击必须可数，不能含糊混成一团：逐次指明每一击（位移／前推／旋转），说明“不是连续震动”，且只能移动相机，绝不让人物变形。

## 为什么能细致重建（以及为什么初次分析会失败）

逆向推导是一个**一对多的逆问题**：不同剧本可能拍出几乎相同的画面。稀疏采样加上“最常见剧情”的先验判断，会悄悄替缺失信息补空——例如把抓住头盔改变朝向的动作看成两个女孩互相喷水，漏看只出现 3–5 帧的画外手，或把相机滚转误认为人物躺下。本技能在建立骨架的阶段保留多种候选剧情，用密帧、状态机分流和音乐音头作为*有判别力的*证据。用户提供的剧情作为行动者清单／意图链，用来缩小假设空间；随后逐帧验证，既不盲从，也不直接否定。

## 目录结构

```
video-to-h3-prompt/
├── SKILL.md                          # 主工作流：5 个通道、6 项检查、10 步标准操作流程、状态机分流
├── README.md
├── LICENSE                           # MIT 许可证
├── agents/openai.yaml                # 智能体元数据
├── references/
│   ├── 01-forensics-commands.md      # 采样／网格／频谱图／RMS／音头／场景切换／时间码 + 核对矩阵
│   ├── 02-h3-field-mapping.md        # 观察结果 -> 14 个字段、时间戳规则、英文组装骨架
│   ├── 03-edit-effects.md            # 定格 4 要素、白闪与漫画与故障效果写法、后期音效
│   ├── 04-coser-replacement.md       # 只换外观的标准操作流程、动作兼容性、道具层级、对照表
│   ├── 05-pitfalls-checklist.md      # 16 个陷阱、六项检查、认知与交付检查清单
│   ├── 06-film-type-and-state-machine.md  # A/B/C 分流、LOCK↔BURST 状态切换、命名机位、可数冲击、滚转、防幻觉骨架
│   ├── 07-onset-and-card-points.md   # 音头检测、自由节奏与固定速度、卡点表、伪切镜过滤、时间码网格
│   ├── 08-multi-image-and-segmentation.md # 多图通用锁定、零外貌规则、>15s 分段、状态交接
│   ├── shot-syntax.md                # Ref2VA 六段式骨架、切镜语法、相机词汇
│   ├── audio-heuristics.md           # 频谱图／RMS／BPM + volumedetect -> 配乐方案表
│   └── ffmpeg-cheatsheet.md          # 以 PowerShell 为主的 ffprobe/ffmpeg 命令参考
└── scripts/
    ├── forensic_probe.ps1            # Windows 一键取证（参数：-Video），包含音头检测
    ├── forensic_probe.sh             # macOS/Linux 一键取证，包含音头检测
    ├── audio_probe.py                # RMS 包络 + 频带能量 + BPM 可信度（numpy；librosa 可选）
    └── onset_probe.py                # 分级音头／卡点检测 + 乐句轮廓（numpy+scipy，无需 librosa）
```

## 依赖要求

- `ffmpeg` / `ffprobe` 须位于 PATH 中。
- `audio_probe.py` 需要 Python 3 和 `numpy`；`onset_probe.py` 还需要 `scipy`。整个流程中 `librosa` 都是可选的——没有它也能分析音头（尤其适用于 Python 3.14 等尚无 librosa wheel 安装包的版本）。

## 快速开始

Windows：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/forensic_probe.ps1 -Video ".\clip.mp4" -OutDir forensic_out
```

macOS / Linux：

```bash
bash scripts/forensic_probe.sh "clip.mp4" forensic_out
```

随后按照 `SKILL.md` 执行：读取 `grid_*.jpg`，对每个有歧义的时间窗口密集抽帧，进行片型分流（`references/06`），将 `onsets.txt` 中的强音头与切镜／击打核对（`references/07`），选择 H3 模式（长片按 `references/08` 拆为 ≤15s 的生成段），然后组装提示词。智能体在回复中打印可直接粘贴的英文文本块（只有用户要求文件交付时才保存为 `.md`）。

## 触发语句

`反推视频` · `视频转H3提示词` · `卡点时间 / 节奏分析` · `多镜头分镜运镜` · `分段生成` · `把图1替换进去 / 只换角色不改剧情` · `reverse this video for h3`（为 H3 反推这条视频） · `extract h3 prompt from this clip`（从这段视频提取 H3 提示词）。

## 配套技能

与 H3 提示词编写／字段参考技能（例如 `h3-prompt-master`）配合使用：本技能产出证据、因果链、状态机决策与卡点；编写技能提供字段定义。输出提示词采用英文；分析可采用用户的语言。

## 许可证

MIT——见 [LICENSE](LICENSE)。
<!-- translation-body:end -->
