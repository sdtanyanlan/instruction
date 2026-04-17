# Linly-Talker Windows 安装使用说明

> 适用环境：Windows 11（64位）+ NVIDIA T1000 8GB 显卡

---

## 一、环境要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 11（64位） |
| 显卡 | NVIDIA T1000 8GB（Turing 架构，支持 CUDA） |
| 显存 | 8 GB |
| 内存 | 建议 16 GB 及以上 |
| Python | 3.10 |
| CUDA | 11.8 |
| 磁盘空间 | 建议预留 30 GB 以上（含模型文件） |

---

## 二、前置准备

### 2.1 安装 NVIDIA 显卡驱动

1. 访问 [NVIDIA 驱动下载页面](https://www.nvidia.cn/Download/index.aspx?lang=cn)
2. 选择以下参数：
   - 产品类型：NVIDIA RTX / Quadro
   - 产品系列：NVIDIA T 系列
   - 产品家族：NVIDIA T1000
   - 操作系统：Windows 11
3. 下载并安装最新驱动，安装完成后**重启电脑**
4. 验证驱动安装：打开命令提示符（Win+R，输入 `cmd`），执行：

```cmd
nvidia-smi
```

若成功显示显卡型号和驱动版本信息，说明驱动安装正常。

> **注意**：NVIDIA T1000 为专业级显卡（Quadro 系列），请在 NVIDIA 官网选择正确的产品系列，不要选择 GeForce 系列。

### 2.2 安装 Git

1. 访问 [Git 官网](https://git-scm.com/download/win) 下载 Windows 安装包
2. 安装时保持默认选项即可
3. 安装完成后在命令提示符中验证：

```cmd
git --version
```

### 2.3 安装 Miniconda（推荐）

1. 访问 [Miniconda 下载页面](https://docs.conda.io/en/latest/miniconda.html) 下载 Windows 安装包（Python 3.10）
2. 安装时勾选 **"Add Miniconda3 to my PATH environment variable"**（或安装后手动配置环境变量）
3. 安装完成后，打开 **Anaconda Prompt**，验证：

```cmd
conda --version
python --version
```

---

## 三、获取项目代码

打开 **Anaconda Prompt**，执行以下命令克隆项目（使用浅克隆以加速下载）：

```cmd
git clone https://github.com/Kedreamix/Linly-Talker.git --depth 1
cd Linly-Talker
git submodule update --init --recursive
```

如果网络访问 GitHub 较慢，也可以在 GitHub 页面直接点击 **"Download ZIP"** 下载后解压。

---

## 四、配置 Python 环境

### 4.1 创建虚拟环境

在 **Anaconda Prompt** 中执行：

```cmd
conda create -n linly python=3.10
conda activate linly
```

### 4.2 安装 PyTorch（CUDA 11.8 版本）

NVIDIA T1000（Turing 架构，计算能力 7.5）完整支持 CUDA 11.8，执行以下命令安装 PyTorch：

```cmd
pip install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu118
```

> **说明**：若需要使用语音克隆（GPT-SoVITS、CosyVoice）等高级功能，推荐此版本。如需更新版本，可将 `cu118` 替换为 `cu121` 并相应调整版本号。

### 4.3 安装 FFmpeg

```cmd
conda install -q ffmpeg==4.2.2
```

### 4.4 升级 pip 并配置国内镜像

```cmd
python -m pip install --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 4.5 安装核心依赖

```cmd
pip install tb-nightly -i https://mirrors.aliyun.com/pypi/simple
pip install -r requirements_webui.txt
```

### 4.6 安装 MuseTalk 相关依赖（可选，用于近实时数字人对话）

```cmd
pip install --no-cache-dir -U openmim
mim install mmengine
mim install "mmcv==2.1.0"
mim install "mmdet>=3.1.0"
mim install "mmpose>=1.1.0"
```

> **注意**：MuseTalk 对显存要求较高，T1000 8GB 显存在运行基础功能时通常足够，但同时开启多个模块时请注意显存占用情况。

### 4.7 验证 CUDA 是否可用

在 **Anaconda Prompt** 中执行：

```cmd
python -c "import torch; print('PyTorch版本:', torch.__version__); print('CUDA可用:', torch.cuda.is_available()); print('GPU型号:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else '无')"
```

正常输出示例：

```
PyTorch版本: 2.0.1+cu118
CUDA可用: True
GPU型号: NVIDIA T1000
```

---

## 五、下载模型文件

Linly-Talker 依赖多个预训练模型，提供以下多种下载途径：

| 渠道 | 地址 |
|------|------|
| 百度云盘 | [下载链接](https://pan.baidu.com/s/1eF13O-8wyw4B3MtesctQyg?pwd=linl)（提取码：`linl`） |
| Hugging Face | [Kedreamix/Linly-Talker](https://huggingface.co/Kedreamix/Linly-Talker) |
| ModelScope | [模型链接](https://www.modelscope.cn/models/Kedreamix/Linly-Talker/summary) |
| 夸克网盘 | [下载链接](https://pan.quark.cn/s/f48f5e35796b) |

**Windows 一键整合包**（包含环境和基础模型）：
[夸克网盘整合包](https://pan.quark.cn/s/cc8f19c45a15)

下载完成后，按照项目目录结构将模型文件放置在 `checkpoints/` 目录下（具体子目录结构请参考项目 [README](https://github.com/Kedreamix/Linly-Talker/blob/main/README.md) 中的 Folder structure 章节）。

---

## 六、配置项目参数

打开项目根目录下的 `configs.py` 文件，根据需要修改以下参数：

```python
# WebUI 服务端口（默认 6006）
port = 6006

# 若需要使用麦克风（实时语音输入），需配置 SSL 证书路径
# ssl_certfile = "path/to/cert.pem"
# ssl_keyfile  = "path/to/key.pem"
```

> **提示**：在 Windows 上通过浏览器使用麦克风时，浏览器要求页面使用 HTTPS 或 localhost。若仅在本地使用，直接访问 `http://localhost:6006` 即可。

---

## 七、启动 WebUI

在 **Anaconda Prompt** 中激活环境并启动：

```cmd
conda activate linly
python webui.py
```

启动成功后，在浏览器中打开：

```
http://localhost:6006
```

即可看到 Linly-Talker 的 Gradio 界面，支持上传图片、文字对话、语音对话等功能。

---

## 八、功能介绍

Linly-Talker 集成了以下核心模块，均可在 WebUI 中选择：

### 8.1 语音识别（ASR）

| 模型 | 说明 |
|------|------|
| Whisper | OpenAI 开源，识别效果好，支持中英文 |
| FunASR | 阿里巴巴开源，速度更快 |
| OmniSenseVoice | 更快速的语音识别模型 |

### 8.2 文字转语音（TTS）

| 模型 | 说明 |
|------|------|
| Edge TTS | 微软在线 TTS，需联网，音质好 |
| PaddleTTS | 百度开源，支持离线使用 |

### 8.3 语音克隆

| 模型 | 说明 |
|------|------|
| GPT-SoVITS | 推荐，仅需 1 分钟音频即可克隆声音 |
| CosyVoice | 高质量 TTS 及声音克隆 |

### 8.4 数字人生成（THG / Avatar）

| 模型 | 说明 |
|------|------|
| SadTalker | 基于单张照片生成说话视频，效果自然 |
| Wav2Lip / Wav2Lipv2 | 嘴唇同步合成，速度较快 |
| MuseTalk | 近实时数字人对话（需额外安装依赖） |

### 8.5 大语言模型（LLM）

| 模型 | 说明 |
|------|------|
| Qwen | 阿里通义千问，支持本地部署 |
| ChatGPT / GeminiPro | 在线 API 调用，需配置 API Key |
| ChatGLM | 智谱开源大模型 |
| GPT4Free | 免费 GPT 代理 |

---

## 九、常见问题

### Q1：`nvidia-smi` 命令无法识别

将 NVIDIA 驱动安装目录下的 `NVSMI` 文件夹添加到系统环境变量 PATH 中，通常路径为：

```
C:\Program Files\NVIDIA Corporation\NVSMI
```

### Q2：`torch.cuda.is_available()` 返回 `False`

- 检查 NVIDIA 驱动是否正确安装（运行 `nvidia-smi` 验证）
- 确认安装的 PyTorch 版本包含 CUDA 支持（`cu118`）
- 尝试重启电脑后再次验证

### Q3：安装依赖包速度很慢

已在步骤 4.4 中配置清华镜像源。若仍然较慢，可单独为某个包指定镜像：

```cmd
pip install <包名> -i https://pypi.tuna.tsinghua.edu.cn/simple
```

conda 包也可配置国内镜像：

```cmd
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

### Q4：运行时出现显存不足（OOM）

NVIDIA T1000 8GB 显存对于运行 SadTalker、Wav2Lip 等基础功能通常足够。若出现 OOM，可尝试：

- 在 WebUI 中不同时加载多个大模型
- 关闭其他占用显存的程序（如游戏、其他深度学习任务）
- 选用显存占用较小的模型（如 Wav2Lip 代替 MuseTalk）

### Q5：`mim install mmcv` 失败

在 Windows 上安装 mmcv 可能遇到编译问题，可尝试安装预编译版本：

```cmd
pip install mmcv==2.1.0 -f https://download.openmmlab.com/mmcv/dist/cu118/torch2.0/index.html
```

### Q6：WebUI 启动后浏览器无法使用麦克风

浏览器安全策略要求非 localhost 页面必须使用 HTTPS 才能访问麦克风。本地使用时直接通过 `http://localhost:6006` 访问即可正常使用麦克风。

### Q7：Windows 上无法执行 `.sh` 脚本（模型下载脚本）

项目提供的 Shell 脚本（如 `scripts/download_models.sh`）在 Windows 上无法直接运行。请手动从上述云盘链接下载模型，或在 Git Bash 中执行脚本：

```bash
bash scripts/download_models.sh
```

---

## 十、参考链接

- 项目 GitHub 主页：[https://github.com/Kedreamix/Linly-Talker](https://github.com/Kedreamix/Linly-Talker)
- 模型下载（ModelScope）：[https://www.modelscope.cn/models/Kedreamix/Linly-Talker/summary](https://www.modelscope.cn/models/Kedreamix/Linly-Talker/summary)
- 模型下载（Hugging Face）：[https://huggingface.co/Kedreamix/Linly-Talker](https://huggingface.co/Kedreamix/Linly-Talker)
- PyTorch 官方安装指南：[https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/)
- NVIDIA 驱动下载：[https://www.nvidia.cn/Download/index.aspx?lang=cn](https://www.nvidia.cn/Download/index.aspx?lang=cn)
- Miniconda 下载：[https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)
- 常见问题汇总：[https://github.com/Kedreamix/Linly-Talker/blob/main/常见问题汇总.md](https://github.com/Kedreamix/Linly-Talker/blob/main/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E6%B1%87%E6%80%BB.md)
