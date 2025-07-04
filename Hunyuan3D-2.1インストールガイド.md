# Hunyuan3D-2.1 Ubuntu環境セットアップ・ワンライナーガイド

**前提条件**: Ubuntu + NVIDIA GPU (VRAM 40GB以上推奨) + NVIDIAドライバ + CUDA Toolkit 12.4

## 1. システム準備
```bash
sudo apt-get update && sudo apt-get install -y git python3.10 python3.10-venv wget
```

## 2. リポジトリクローン & 仮想環境作成
```bash
git clone https://github.com/Tencent-Hunyuan/Hunyuan3D-2.1.git && cd Hunyuan3D-2.1 && python3.10 -m venv venv && source venv/bin/activate
```

## 3. 全依存関係インストール (ワンライナー)
```bash
pip install --upgrade pip && pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu124 && pip install -r requirements.txt && (cd hy3dpaint/custom_rasterizer && pip install -e .) && (cd hy3dpaint/DifferentiableRenderer && bash compile_mesh_painter.sh) && mkdir -p hy3dpaint/ckpt && wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.0/RealESRGAN_x4plus.pth -O hy3dpaint/ckpt/RealESRGAN_x4plus.pth
```

## 4. 追加パッケージインストール (ワンライナー)
```bash
pip install omegaconf hydra-core pyyaml einops transformers diffusers accelerate xformers timm safetensors huggingface-hub datasets tokenizers trimesh gradio numpy pillow opencv-python matplotlib scipy scikit-image open3d rembg plyfile imageio tqdm wandb tensorboard pymeshlab pygltflib onnxruntime xatlas
```

## 5. Gradio Web UI起動
```bash
python gradio_app.py
python3 gradio_app.py
```

```bash
python3 gradio_app.py --model_path tencent/Hunyuan3D-2.1 --subfolder hunyuan3d-dit-v2-1 --texgen_model_path tencent/Hunyuan3D-2.1 --low_vram_mode
```

**アクセス**: ブラウザで `http://127.0.0.1:7860` を開く

**注意**: 仮想環境を再有効化する場合は `cd Hunyuan3D-2.1 && source venv/bin/activate`
