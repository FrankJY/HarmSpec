# HarmSpec demo — GitHub Pages (方案 A: `/docs`)

静态试听页生成在 **`docs/index.html`**，音频在 **`docs/audio/`**。

## 1. 在本机生成试听包

```bash
/21SA027/conda_envs/ss-mpe/bin/python \
  /21SA027/DDSP_Mixture_Model/mt3_harm_spec/scripts/build_harmspec_demo_site.py \
  --device cuda:0
```

本地预览（在仓库根目录 `DDSP_Mixture_Model` 下）：

```bash
cd /21SA027/DDSP_Mixture_Model
python3 -m http.server 8765 --directory docs
```

浏览器打开：`http://127.0.0.1:8765/`

仅重新打包（不重跑 GPU 导出）：

```bash
.../build_harmspec_demo_site.py --skip-export
```

## 2. 把仓库推到 GitHub

若还没有远程仓库：

1. 在 [github.com/new](https://github.com/new) 新建仓库（例如 `DDSP_Mixture_Model` 或 `HarmSpec`），**不要**勾选 “Add a README”（避免首次 push 冲突）。
2. 在终端进入本地仓库根目录（包含 `docs/` 的那一层）：

```bash
cd /21SA027/DDSP_Mixture_Model
git init   # 若尚未 init
git add docs/
git add mt3_harm_spec/scripts/build_harmspec_demo_site.py
git add mt3_harm_spec/scripts/export_urmp_z15_listen_demo_v0.py
# 若希望整仓上传：git add .  （注意 .gitignore 排除 ckpt/大数据）
git commit -m "Add HarmSpec GitHub Pages audio demo"
git branch -M main
git remote add origin git@github.com:YOUR_USER/YOUR_REPO.git
git push -u origin main
```

> **体积**：`docs/audio/` 约几十 MB。若 push 失败或仓库过大，可对 `docs/audio/**/*.wav` 配置 [Git LFS](https://git-lfs.com/)，或只提交 demo 用到的 3×3 clip。

### Git LFS（可选）

```bash
git lfs install
git lfs track "docs/audio/**/*.wav"
git add .gitattributes
git add docs/audio/
git commit -m "Track demo wavs with Git LFS"
git push
```

## 3. 开启 GitHub Pages

1. 打开 GitHub 上该仓库 → **Settings** → 左侧 **Pages**。
2. **Build and deployment** → **Source** 选 **Deploy from a branch**。
3. **Branch** 选 `main`（或你的默认分支），文件夹选 **`/docs`**，点 **Save**。
4. 等待 1–3 分钟，Pages 会显示站点 URL，形如：  
   `https://YOUR_USER.github.io/YOUR_REPO/`

## 4. 更新试听内容

改 checkpoint 或 clip 后重新跑 `build_harmspec_demo_site.py`，然后：

```bash
git add docs/
git commit -m "Refresh HarmSpec audio demo"
git push
```

Pages 会自动用新 commit 重建（稍等片刻刷新浏览器）。

## 5. 主表 checkpoint 对照

| 区块 | mix-only | stem-supervised |
|------|----------|-----------------|
| Slakh 11 / paper-3 | `slakh_gt_maestro_z11_2gpu_mixonly_server/best.pth` | `slakh_gt_maestro_z11_4gpu_dc4_server/best.pth` |
| URMP | `urmp_z15_mixonly_from_synthsod_mixonly_1gpu/best.pth` | `urmp_z15_from_synthsod/best.pth` |

URMP 曲目：**17, 35, 36**（各中心 4 s）。