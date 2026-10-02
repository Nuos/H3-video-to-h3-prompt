# 视频逆向分析 FFmpeg 速查表（简体中文）

来源：[`ffmpeg-cheatsheet.md`](ffmpeg-cheatsheet.md)  
源版本：`7371c753ff4a087ca411e12033a3314231bd28c8`

## 概念说明

本模块提供视频逆向分析中的常用命令：检查元数据、按时间采样帧、对转场进行密集抽帧、制作网格缩略图总览表、导出音频、绘制声谱图，以及通过相邻帧差分寻找硬切点。稀疏抽帧用于建立全片时间骨架，密集抽帧用于补查变化快速或含义不清的片段。

全部命令、参数、路径、字段和数字保持原样，仅翻译代码注释中的自然语言。命令以 Windows PowerShell 为前提，`<video.mp4>` 等仍是原文中的输入占位符；复制使用时需按原文说明替换。下文是对源文档的完整翻译，并未实际运行命令。

## 术语与重要单词释义

IPA 采用常见英式读音；`n.` 表示名词，`v.` 表示动词，`adj.` 表示形容词，`phr.` 表示短语，`abbr.` 表示缩写，`prop. n.` 表示专有名词。专名、缩写和代码标识符未给出统一标准读音时明确不编造 IPA。

| 英文 | IPA 音标 | 词性缩写 | 语境中文含义 |
|---|---|---|---|
| reverse-engineering | /rɪˌvɜːs ˌendʒɪˈnɪərɪŋ/ | n. | 逆向分析；根据成片推断镜头、时间与制作结构 |
| cheatsheet | /ˈtʃiːt ʃiːt/ | n. | 速查表；便于快速查阅的命令清单 |
| probe | /prəʊb/ | v., n. | 探查；读取媒体元数据的检查操作 |
| metadata | /ˈmetədeɪtə/ | n. | 元数据；描述媒体时长、编码、尺寸等属性的数据 |
| frame | /freɪm/ | n. | 帧；视频中的单幅图像 |
| extract | /ɪkˈstrækt/ | v. | 提取；从视频中导出帧或音频 |
| interval | /ˈɪntəvəl/ | n. | 间隔；相邻抽样时间点之间的时长 |
| sparse | /spɑːs/ | adj. | 稀疏的；抽取较少帧以观察整体结构 |
| dense | /dens/ | adj. | 密集的；提高单位时间内的抽帧数量 |
| transition | /trænˈzɪʃən/ | n. | 转场；镜头或画面状态之间的变化 |
| seek | /siːk/ | v., n. | 定位；跳转到媒体中的某个时间位置 |
| codec | /ˈkəʊdek/ | n. | 编解码器；处理音视频编码或解码的机制 |
| bitrate | /ˈbɪtreɪt/ | n. | 码率；单位时间内传输或存储的数据量 |
| contact sheet | /ˈkɒntækt ʃiːt/ | phr. | 缩略图总览表；将多帧缩略图排列成网格的总览图 |
| spectrogram | /ˈspektrəɡræm/ | n. | 声谱图；显示频率和能量随时间变化的图像 |
| hard cut | /hɑːd kʌt/ | phr. | 硬切；不经过渐变等过渡而直接切换镜头 |
| composition | /ˌkɒmpəˈzɪʃən/ | n. | 构图；画面中主体与空间元素的组织方式 |
| focal length | /ˈfəʊkəl leŋθ/ | phr. | 焦距；影响视角和画面透视呈现的镜头参数 |
| padding | /ˈpædɪŋ/ | n. | 留边；网格中帧与帧之间的间隙 |
| anchor | /ˈæŋkə/ | n. | 锚点；用于约束或参照生成结果的画面 |
| FFmpeg / ffprobe / PowerShell / Windows / bash | 专名或程序名；此处不编造 IPA | prop. n. | 音视频处理工具、媒体信息探查工具、命令行环境和操作系统 |
| H3 / I2VA / L2VA / RMS / BPM / fps | 缩写或模型名；此处不编造 IPA | abbr. | H3 模型；I2VA／L2VA 生成模式标记；均方根；每分钟拍数；每秒帧数。原文未展开 I2VA／L2VA 全称 |
| `duration` / `r_frame_rate` / `nb_frames` / `codec_name` / `has_b_frames` | 字段标识符，不编造 IPA | identifier | 时长／帧率／帧数／编解码器名称／是否存在 B 帧的相关元数据字段 |
| `-ss` / `-i` / `-noaccurate_seek` / `NUL` | CLI 参数或设备标识符，不编造 IPA | identifier | 时间定位／输入／关闭精确定位／Windows 空设备 |

## 完整正文译文

<!-- translation-body:start -->
# 用于视频逆向分析的 FFmpeg 速查表

所有命令均假定使用 **Windows 上的 PowerShell**（反引号 `` ` `` 用于续行）。在 bash 中使用时，需要更换循环写法，并将 `NUL`→`/dev/null`。完整的首轮处理流程也封装在 `scripts/forensic_probe.ps1` 中。

## 探查视频元数据

```powershell
ffprobe -v error -show_format -show_streams -of json "<video.mp4>"
```

单行摘要：

```powershell
ffprobe -v error -show_entries format=duration,bit_rate `
  -show_entries stream=codec_type,codec_name,width,height,r_frame_rate,nb_frames,sample_rate,channels `
  -of default=nw=1 "<video.mp4>"
```

影响后续判断的六个字段：`duration`（最终 H3 时长，±0.02）、`width×height`（竖屏还是横屏）、`r_frame_rate`、`nb_frames`（用时长×fps 做合理性检查）、`codec_name`、`has_b_frames`（>0 → 添加 `-noaccurate_seek`，使定位更干净）。

## 按固定间隔抽帧（稀疏抽样，建立骨架）

```powershell
$interval = 0.2; $count = 73; $out = "D:\path\frames"
New-Item -ItemType Directory -Force -Path $out | Out-Null
for ($i=0; $i -le $count; $i++) {
  $t = "{0:F3}" -f ($i * $interval)
  ffmpeg -y -ss $t -i "<video.mp4>" -frames:v 1 `
         -vf "scale=iw/2:-1" -q:v 3 "$out\f_$i.jpg" -loglevel error
}
```

按片长选择间隔：<5s→0.2s；5–10s→0.3–0.4s；10–15s→0.15–0.2s；>15s→0.2s，并在转场处定向补抽帧。提升质量最有效的一项操作是：**将间隔减半，重新抽取此前漏掉的转场**。

## 连续帧率抽帧（对转场进行密集抽样）

```powershell
# 全片按 2fps 抽帧，宽度为 480
ffmpeg -y -i "<video.mp4>" -vf "fps=2,scale=480:-1" frames/f_%02d.jpg
# 对含义不明确的 2.5s 时间窗口按 4fps 抽帧（必要时用 8fps）
ffmpeg -y -ss 8.5 -t 2.5 -i "<video.mp4>" -vf "fps=4,scale=360:-1" seg/c_%02d.jpg
```

## 提取精确时刻的单帧（全分辨率最终检查）

```powershell
ffmpeg -y -ss 9.800 -i "<video.mp4>" -frames:v 1 -q:v 2 "key_9_8.jpg"
```

## 缩略图总览表／网格图（一次查看整条时间线）

```powershell
# 4x4 网格，黄色留边使阅读顺序清晰可辨
ffmpeg -y -framerate 1 -i frames/f_%02d.jpg `
  -vf "scale=240:-2,tile=4x4:padding=4:color=yellow" grid_%03d.jpg
# 密集抽样窗口采用 5x2 网格
ffmpeg -y -framerate 1 -i seg/c_%02d.jpg `
  -vf "scale=200:355,tile=5x2:padding=3:color=yellow" seg/c_grid.jpg
```

输入帧超过 16 张 → ffmpeg 会输出多张 `grid_001/002...` 网格图。

## 导出音频 + 声谱图

```powershell
ffmpeg -y -i "<video.mp4>" -vn -ac 1 -ar 22050 audio.wav         # 用于音频分析的规格
ffmpeg -y -i audio.wav -lavfi showspectrumpic=s=1000x400:legend=1 spectrum.jpg
ffmpeg -y -i "<video.mp4>" -vn -ac 1 -ar 8000 -f wav out8k.wav   # 用于响度检测的规格
ffmpeg -i out8k.wav -af "volumedetect" -f null NUL 2>&1 | Select-String volume
python scripts/audio_probe.py audio.wav                          # RMS + BPM 可信度
```

## 视觉差分序列（自动定位硬切点）

```powershell
New-Item -ItemType Directory -Force -Path diff | Out-Null
for ($i=1; $i -le 73; $i++) {
  $prev = $i - 1
  ffmpeg -y -i "f_$i.jpg" -i "f_$prev.jpg" `
         -filter_complex "blend=all_mode=difference" -frames:v 1 "diff/d_$i.jpg"
}
```

接近全黑的 `diff` = 画面相同（连续）；明亮的输出 = 硬切。构图跳变（角度／焦距）也会表现为切镜；平滑的推镜／拉镜／摇镜属于相机运动，不是切镜。

## 首帧与尾帧（I2VA / L2VA 锚点）

```powershell
ffmpeg -y -i "<video.mp4>" -vf "select=eq(n\,0)" -frames:v 1 first.jpg
$dur = (ffprobe -v error -show_entries format=duration -of csv=p=0 "<video.mp4>")
ffmpeg -y -ss $dur -i "<video.mp4>" -frames:v 1 last.jpg
```

## 常见故障情形

| 症状 | 原因 | 解决方法 |
|---|---|---|
| `q=0`，且 `-q:v` 被忽略 | 输出格式为 `.png` | 使用 `.jpg` |
| 开头出现黑帧 | `-ss` 定位参数的位置 | 将 `-ss` 移到 `-i` 之后以提高精度，或使用 `-noaccurate_seek` |
| 写入帧时出现 `Permission denied`（权限被拒绝） | 目录不存在 | 先执行 `New-Item -ItemType Directory -Force` |
| 帧偏移约 1s | 输入侧定位与输出侧定位的差异 | 添加 `-noaccurate_seek`，或将 `-ss` 移到 `-i` 之后 |
| 导出的音频无声 | 没有音频流 | 检查 `ffprobe -show_streams`；若没有 `codec_type: audio`，跳过音频分析 |
| 网格图只显示前 16 帧 | tile 固定为 4x4 | 使用 `grid_%03d.jpg` 命名模式输出多张网格图 |
| 带空格的路径报错 | 缺少引号 | 始终为完整路径加引号 |
<!-- translation-body:end -->
