# 01 — 取证命令库与音画交叉验证（简体中文完整译本）

来源：`references/01-forensics-commands.md`  
源版本：`7371c753ff4a087ca411e12033a3314231bd28c8`  
译文说明：原文保留；以下完整正文逐源行对应翻译，代码、命令、路径、字段、时间码及数值保持原样。命令注释中的自然语言已翻译。

## 概念说明

本模块的“取证”指借助媒体元信息、抽帧、频谱、音量能量包络及音头检测，为视频中的动作、剪辑和声音建立可核对的证据。稀疏抽帧用于建立全片结构，密集抽帧用于辨别转折及硬切，音画交叉验证则把听到的重音与看到的事件对应起来。音头、能量峰和音乐节拍是不同概念，不能把随机声响的峰值直接当成稳定节拍。

## 术语及重要单词释义

音标采用常见英语读音；`n.` 为名词，`v.` 为动词，`adj.` 为形容词，`abbr.` 为缩写，`prop. n.` 为专名，`id.` 为代码或字段标识符。专名、工具名、缩写及标识符未采用标准词典读音时明确不编 IPA。

| 英文/标识符 | IPA 音标 | 词性 | 本模块语境中的中文含义 |
|---|---|---|---|
| forensic | /fəˈrensɪk/ | adj. | 取证的；依据媒体证据进行核验的 |
| frame | /freɪm/ | n. | 视频的一帧画面 |
| contact sheet | /ˈkɒntækt ʃiːt/ | n. | 缩略图总览表（接触印样表）；将多张抽帧图排成网格以集中核对 |
| burst | /bɜːst/ | n., v. | 突发、爆发；例如喷溅或能量集中释放 |
| spectrum | /ˈspektrəm/ | n. | 频谱；声音能量在各频率上的分布 |
| envelope | /ˈenvələʊp/ | n. | 包络；能量随时间变化的整体轮廓 |
| onset | /ˈɒnset/ | n. | 音头；声音事件开始或能量明显上升的位置 |
| kick | /kɪk/ | n. | 底鼓；频谱上可用于判断规则节拍的低频打击声 |
| tempo | /ˈtempəʊ/ | n. | 音乐速度；这里是自相关估计出的参考速度 |
| cut | /kʌt/ | n., v. | 切镜、剪切；硬切是画面直接切换到另一镜头 |
| recipe | /ˈresəpi/ | n. | 配方；可复用的一组配乐设计或处理条件 |
| probe | /prəʊb/ | n., v. | 探测；读取媒体信息或分析信号 |
| rubato | /ruːˈbɑːtəʊ/ | n. | 自由速度、弹性速度；速度随表达伸缩，不宜当作固定节拍 |
| RMS / BPM / BGM / RGB / S / H / fps | 不编 IPA：缩写或检测标签，本模块未给出统一标准读音 | abbr. | 均方根／每分钟拍数／背景音乐／红绿蓝／强音头／硬音头／每秒帧数 |
| Windows / PowerShell / macOS / Linux / bash | 不编 IPA：操作系统或命令解释器专名 | prop. n. | 文中使用的运行环境；命令名称保持原样 |
| ffmpeg / ffprobe / librosa / numpy / scipy | 不编 IPA：工具或软件包标识符 | id. | 音视频处理、媒体探测及音频数值分析所用工具或库 |
| duration / width / height / r_frame_rate / codec / sample_rate / channels | 不编 IPA：媒体元信息字段 | id. | 时长／宽度／高度／帧率字段／编码格式／采样率／声道数 |
| onset std / onset mean / scene / pts_time / volumedetect | 不编 IPA：统计表达式或滤镜、输出字段 | id. | 音头统计量的标准差／均值、场景变化分数、时间戳、响度检测滤镜 |
| editing / camera / integrated / soundscape / music / lighting | 不编 IPA：映射表中的字段或字段简写 | id. | 剪辑／摄影／综合描述／声景／音乐／照明；保留原文简写 |
| fully_copy / non_diegetic_music / boom | 不编 IPA：复制策略、字段或声音提示标记 | id. | 完整复用／叙事空间外的配乐／低沉冲击声；保留其标记形式 |

## 完整正文译文

<!-- translation-body:start -->
# 01 — 取证命令库与音画交叉验证

跨平台：Windows 用 PowerShell（本仓库 `ffmpeg-cheatsheet.md` 亦为 PowerShell 风格），macOS/Linux 用 bash。`IN.mp4` 替换为实际视频，路径含空格/中文必须加引号（中文路径建议先复制为纯英文副本，见 §12）。

## 1. 媒资建档

```powershell
ffprobe -v error -show_format -show_streams -of json "IN.mp4"
```

记录：duration（时长） / width×height（宽×高） / r_frame_rate（帧率字段） / 视频编码 / 音轨（codec〔编码格式〕、sample_rate〔采样率〕、channels〔声道数〕）。竖边更长=9:16，横边更长按 16:9、4:3 归类。

## 2. 全片稀疏抽帧（骨架层）

```powershell
# 2fps、统一宽 480
ffmpeg -y -i "IN.mp4" -vf "fps=2,scale=480:-1" frames/f_%02d.jpg
```

按片长定间隔：<5s→0.2s；5–10s→0.3–0.4s；10–15s→0.15–0.2s；>15s→0.2s + 转折补抽。

## 3. 拼缩略图总览表，一次看全

```powershell
# 4 列 4 行，黄边便于读序；超过 16 帧会输出 grid_001/002 ...
ffmpeg -y -framerate 1 -i frames/f_%02d.jpg -vf "scale=240:-2,tile=4x4:padding=4:color=yellow" grid_%03d.jpg
```

## 4. 转折区间密帧（判别层）

```powershell
# -ss 起点 -t 持续；4fps，判别爆发/掰头/火光时上 fps=8
ffmpeg -y -ss 8.5 -t 2.5 -i "IN.mp4" -vf "fps=4,scale=360:-1" seg/c_%02d.jpg
ffmpeg -y -framerate 1 -i seg/c_%02d.jpg -vf "scale=200:355,tile=5x2:padding=3:color=yellow" seg/c_grid.jpg
```

## 5. 原分辨率单帧终核

```powershell
ffmpeg -y -ss 9.8 -i "IN.mp4" -frames:v 1 -q:v 2 key_9_8.jpg
```

## 6. 提音频（22.05k 单声道足够分析；只做响度时 8k 即可）

```powershell
ffmpeg -y -i "IN.mp4" -vn -ac 1 -ar 22050 audio.wav
```

## 7. 频谱图（判音乐与现场声）

```powershell
ffmpeg -y -i audio.wav -lavfi showspectrumpic=s=1000x400:legend=1 spectrum.jpg
```

- **等间隔等宽低频竖纹贯穿全片** → 鼓机/节拍，存在 BGM（背景音乐）；
- **宽带随机能量 + 偶发上扬谐波曲线** → 人声说/笑/喊；
- **全频段持续铺底** → 环境底噪（街道、风、发动机）；
- **定格/喷溅/命中瞬间的竖向亮块** → 瞬时音效，与画面事件对齐。

## 8. RMS（均方根）能量包络 + BPM（每分钟拍数）可信度（粗粒度：哪里响）

```powershell
python scripts/audio_probe.py audio.wav
```

**BPM 误检判据（同时满足判"无稳定 BGM"）**：
1. `onset std / onset mean > 0.8`（音头统计量的标准差与均值之比大于 0.8，节拍不规律）；
2. 频谱无等间隔底鼓网格；
3. RMS 高峰是离散瞬时峰而非周期性起伏。
三条齐备时 librosa 的 BPM 是把随机音头误当节拍，结论写「无稳定 BGM」。

## 9. 音头检测 + 卡点（细粒度：每个重音在哪一帧）

RMS 0.5s 太粗，卡点剪辑（切镜/定格/命中/爆发对齐音乐）必须用音头：

```powershell
python scripts/onset_probe.py audio.wav
python scripts/onset_probe.py audio.wav --csv onsets.csv
```

输出：强(S)/硬(H)音头时间（卡点候选）、`.` 轮指弱音、逐秒密度/强度轮廓（乐句形状）、自相关参考速度。**自由散板/轮指独奏（琵琶等）的速度估计置信度低，不可当节拍器**。详见 `07-onset-and-card-points.md`。该脚本仅依赖 numpy+scipy，无需 librosa。

## 10. 硬切检测与"火光伪切镜"排除

```powershell
# scene 阈值 0.4 找硬切（0.3 宽松找候选）
ffmpeg -i "IN.mp4" -filter:v "select='gt(scene,0.4)',showinfo" -f null NUL 2>&1 | Select-String "pts_time"
```

**枪口火光、爆炸闪光、能量光束、白闪会被误报成切镜**（亮度突变但机位连续）。每个候选切点密帧核对：闪光前后机位/角度/景别连续＝同一镜头；只有机位真正跳变才算硬切。战斗片齐射瞬间常出现一簇伪切镜，不照单全收。最终 `镜头数 = 真硬切数 + 1`。

## 11. 烧时间码的缩略图总览表（机位/卡点一图核对）

```powershell
# 4fps、左上烧时间码、拼 4x4（每张覆盖 4 秒），逐段定机位再与音头对账
ffmpeg -y -i "IN.mp4" -vf "fps=4,scale=320:-1,drawtext=text='%{pts\:hms}':x=6:y=6:fontcolor=yellow:fontsize=18:box=1:boxcolor=black@0.6,tile=4x4:padding=3:color=yellow" -vsync 0 sb_%02d.jpg
```

## 12. 响度补充判据（volumedetect）

```powershell
ffmpeg -y -i "IN.mp4" -vn -ac 1 -ar 8000 -f wav out8k.wav
ffmpeg -i out8k.wav -af "volumedetect" -f null NUL 2>&1 | Select-String volume   # PowerShell
# bash: ... -f null - 2>&1 | grep volume
```

平均/最大音量与配乐配方的对应见 `audio-heuristics.md`。

## 13. 音画交叉验证矩阵（工作模板）

| RMS 峰 / 强音头时刻 | 密帧画面证据（谁先动/方向/结果/机位） | 候选解释 → 裁决结论 | 落点字段 |
|---|---|---|---|
| 1.75s（S 音头） | 反打受害者正面警觉，机位跳变 | 连续运镜？×／硬切？√ | editing(cut) + camera |
| 6.6–7.0s（S 音头 + RMS 峰） | 枪口火舌，随后贴臀机位冒火花 | 火光伪切镜？√（机位连续）／真切？× | integrated + soundscape |
| 11–12.5s（密集 S/H 音头 + RMS 最高峰） | 双枪齐射、高俯拍趴地连中 | 火力高潮，一串卡点 | integrated + editing + music |
| 22.55s（H 音头） | 硬切背肩近景、转暖调、开始回头 | 关键硬切（情绪转场）√ | editing + camera + lighting |
| 28.51s（S 音头） | 白光 + RGB 故障 + 黑翼爆发 | 后期特效爆发，对齐尾音 √ | editing(特效) + soundscape(boom) |
| 全程 | 音头间隔不匀、无底鼓网格 | 72.8BPM 误检 → 自由散板，不写 BPM | non_diegetic_music=fully_copy+rubato |

规则：**每个硬切/定格/冲击/爆发都要在 ±0.1s 内找到 S/H 音头；每个 RMS 峰都要在画面里找到成因；每个关键动作都要在音频里找到印证**。对不上的行回 §4 密帧或进入提问流程。

## 14. 一键取证

```powershell
# Windows PowerShell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/forensic_probe.ps1 -Video "IN.mp4" -OutDir forensic_out
```
```bash
# macOS / Linux
bash scripts/forensic_probe.sh "IN.mp4" forensic_out
```

产出：2fps 帧、全片网格、audio.wav、频谱、媒体探测信息、RMS/BPM 表与 **音头卡点表**。之后人工进入密帧判别、片型分流（`06`）与因果重建。
<!-- translation-body:end -->
