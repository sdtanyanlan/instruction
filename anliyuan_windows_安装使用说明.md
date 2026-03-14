# Ultralight Digital Human（anliyuan）Windows 安装使用说明

> 适用环境：Windows 10/11（64位）+ NVIDIA T1000 8GB 显卡

---

## 一、环境要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10 / 11（64位） |
| 显卡 | NVIDIA T1000 8GB（Turing 架构，支持 CUDA） |
| 显存 | 8 GB |
| 内存 | 建议 16 GB 及以上 |
| Python | 3.10 |
| CUDA | 11.7 |
| 磁盘空间 | 建议预留 20 GB 以上 |

---

## 二、前置准备

### 2.1 安装 NVIDIA 显卡驱动

1. 访问 [NVIDIA 驱动下载页面](https://www.nvidia.cn/Download/index.aspx?lang=cn)
2. 选择以下参数：
   - 产品类型：NVIDIA RTX / Quadro
   - 产品系列：NVIDIA T 系列
   - 产品家族：NVIDIA T1000
   - 操作系统：Windows 10 64-bit 或 Windows 11
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

### 2.4 安装 FFmpeg

推理结束后需要用 FFmpeg 合并音视频：

1. 访问 [FFmpeg 下载页面](https://www.gyan.dev/ffmpeg/builds/)，下载 `ffmpeg-release-essentials.zip`
2. 解压到指定目录，例如 `C:\ffmpeg`
3. 将 `C:\ffmpeg\bin` 添加到系统环境变量 PATH 中
4. 验证：

```cmd
ffmpeg -version
```

---

## 三、获取项目代码

打开 **Anaconda Prompt**，执行以下命令克隆项目：

```cmd
git clone https://github.com/anliyuan/Ultralight-Digital-Human.git
cd Ultralight-Digital-Human
```

如果网络访问 GitHub 较慢，也可以在 GitHub 页面直接点击 **"Download ZIP"** 下载后解压。

---

## 四、配置 Python 环境

### 4.1 创建虚拟环境

在 **Anaconda Prompt** 中执行：

```cmd
conda create -n dh python=3.10
conda activate dh
```

### 4.2 安装 PyTorch（CUDA 11.7 版本）

NVIDIA T1000 支持 CUDA 11.7，执行以下命令安装 PyTorch：

```cmd
conda install pytorch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 pytorch-cuda=11.7 -c pytorch -c nvidia
conda install mkl=2024.0
```

> **说明**：NVIDIA T1000（Turing 架构，计算能力 7.5）完整支持 CUDA 11.7，安装上述版本无兼容性问题。

### 4.3 安装其他依赖库

```cmd
pip install opencv-python
pip install transformers
pip install numpy==1.23.5
pip install soundfile
pip install librosa
pip install onnxruntime
```

### 4.4 验证 CUDA 是否可用

在 **Anaconda Prompt** 中执行：

```cmd
python -c "import torch; print('PyTorch版本:', torch.__version__); print('CUDA可用:', torch.cuda.is_available()); print('GPU型号:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else '无')"
```

正常输出示例：

```
PyTorch版本: 1.13.1
CUDA可用: True
GPU型号: NVIDIA T1000
```

---

## 五、下载模型文件

下载 Wenet 编码器 ONNX 模型：

1. 访问以下链接下载 `encoder.onnx`：  
   [https://drive.google.com/file/d/1e4Z9zS053JEWl6Mj3W9Lbc9GDtzHIg6b/view?usp=drive_link](https://drive.google.com/file/d/1e4Z9zS053JEWl6Mj3W9Lbc9GDtzHIg6b/view?usp=drive_link)

2. 将下载的 `encoder.onnx` 文件放入项目的 `data_utils/` 目录下：

```
Ultralight-Digital-Human\
└── data_utils\
    └── encoder.onnx   ← 放在这里
```

---

## 六、准备训练视频

- 时长：**3～5 分钟**
- 要求：
  - 每一帧画面中都要有**完整的人脸**
  - 音频清晰，无明显噪音和回声（建议使用外接麦克风录制）
  - 建议视频开头 **20 秒不说话**（用于流式推理的静音素材）
- 帧率：
  - 使用 **Wenet** 音频编码器：视频帧率必须为 **20fps**
  - 使用 **HuBERT** 音频编码器：视频帧率必须为 **25fps**

将视频文件放入一个新建的文件夹，例如 `data_dir\`。

---

## 七、数据预处理

进入项目目录，激活环境后执行预处理脚本：

```cmd
conda activate dh
cd Ultralight-Digital-Human\data_utils

:: 使用 HuBERT（效果更好）
python process.py ..\data_dir\your_video.mp4 --asr hubert

:: 或使用 Wenet（速度更快，可在移动端实时运行）
:: python process.py ..\data_dir\your_video.mp4 --asr wenet
```

> 将 `your_video.mp4` 替换为你的实际视频文件名。处理过程需要一段时间，请耐心等待。

---

## 八、训练模型

### 8.1 训练 SyncNet（可选，提升效果）

```cmd
cd ..
python syncnet.py --save_dir ./syncnet_ckpt/ --dataset_dir ./data_dir/ --asr hubert
```

训练完成后，从 `syncnet_ckpt/` 中找到 **loss 最低** 的 checkpoint 文件，记下文件名（如 `syncnet_ckpt/xxx.pth`）。

### 8.2 训练数字人模型

```cmd
python train.py --dataset_dir ./data_dir/ --save_dir ./checkpoint/ --asr hubert --use_syncnet --syncnet_checkpoint syncnet_ckpt/xxx.pth
```

> 将 `syncnet_ckpt/xxx.pth` 替换为实际的 syncnet checkpoint 路径。  
> 如果跳过 SyncNet，去掉 `--use_syncnet --syncnet_checkpoint` 参数即可。

训练过程中，T1000 8GB 显存完全满足需求，GPU 利用率较高是正常现象。

---

## 九、推理（生成数字人视频）

### 9.1 提取测试音频特征

准备一段 WAV 格式的测试音频（**采样率必须为 16000 Hz**）。

```cmd
:: 使用 HuBERT
python data_utils/hubert.py --wav your_test_audio.wav

:: 使用 Wenet
:: python data_utils/wenet_infer.py your_test_audio.wav
```

执行后会生成 `your_test_audio_hu.npy`（HuBERT）或 `your_test_audio_wenet.npy`（Wenet）。

### 9.2 生成视频

```cmd
python inference.py --asr hubert --dataset ./data_dir/ --audio_feat your_test_audio_hu.npy --save_path output.mp4 --checkpoint ./checkpoint/your_trained_ckpt.pth
```

> 将 `your_trained_ckpt.pth` 替换为实际训练得到的 checkpoint 文件名。

### 9.3 合并音视频

```cmd
ffmpeg -i output.mp4 -i your_test_audio.wav -c:v libx264 -c:a aac result_final.mp4
```

最终生成的 `result_final.mp4` 即为带有音频的数字人视频。

---

## 十、常见问题

### Q1：`nvidia-smi` 命令无法识别

将 NVIDIA 驱动安装目录下的 `NVSMI` 文件夹添加到系统环境变量 PATH 中，通常路径为：
```
C:\Program Files\NVIDIA Corporation\NVSMI
```

### Q2：`torch.cuda.is_available()` 返回 `False`

- 检查 NVIDIA 驱动是否正确安装（运行 `nvidia-smi` 验证）
- 确认安装的 PyTorch 版本包含 CUDA 支持（`pytorch-cuda=11.7`）
- 尝试重启电脑后再次验证

### Q3：安装 PyTorch 时速度很慢

可以配置 conda 国内镜像源，在 Anaconda Prompt 中执行：

```cmd
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

### Q4：pip 安装速度很慢

使用清华镜像源加速：

```cmd
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple <包名>
```

### Q5：训练时出现显存不足（OOM）

NVIDIA T1000 8GB 显存通常足够运行此项目。如果出现 OOM，可以尝试：
- 减小训练的 batch size（修改 `train.py` 中的 `--batch_size` 参数）
- 关闭其他占用显存的程序

### Q6：视频效果不好

- 确认训练视频质量满足要求（清晰、无噪、完整人脸）
- 测试音频采样率须为 16000 Hz
- 音频质量差（噪声、回音、人声不清晰）会严重影响效果，建议使用外接麦克风重新录制视频

---

## 十一、参考链接

- 项目 GitHub 主页：[https://github.com/anliyuan/Ultralight-Digital-Human](https://github.com/anliyuan/Ultralight-Digital-Human)
- PyTorch 官方安装指南：[https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/)
- NVIDIA 驱动下载：[https://www.nvidia.cn/Download/index.aspx?lang=cn](https://www.nvidia.cn/Download/index.aspx?lang=cn)
- Miniconda 下载：[https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)
- FFmpeg 下载：[https://www.gyan.dev/ffmpeg/builds/](https://www.gyan.dev/ffmpeg/builds/)
