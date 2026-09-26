# evergo ReSukiSU 内核自动编译

红米 Note 11 5G（evergo，MT6833，内核 4.14）自动编译带 **ReSukiSU** 的内核，通过 GitHub Actions 云端编译，无需本地环境。

- 内核源码：`szh319/android_kernel_xiaomi_mt6833_evergo`（4.14.336，MIUI 13/14 可用）
- Root 方案：ReSukiSU（kprobes hook 模式，内核已自带 `CONFIG_KPROBES=y`，无需手动打 hook 补丁）
- 产物：AnyKernel3 卡刷包，写入 `boot` 分区

## 使用步骤

1. 在 GitHub 上**新建一个空仓库**（Public/Private 均可，例如 `evergo-resukisu-build`），**不要**勾选自动生成 README。
2. 把本文件夹（`.github` 目录 + 本 README）推送到该仓库：
   ```powershell
   cd evergo-resukisu
   git init
   git add .
   git commit -m "evergo ReSukiSU build"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/evergo-resukisu-build.git
   git push -u origin main
   ```
   （也可以用 GitHub 网页端直接上传文件：Add file → Upload files，注意 `.github/workflows/build-resukisu.yml` 的路径层级要一致）
3. 进入仓库的 **Actions** 页面 → 若提示启用，点击 **I understand my workflows, go ahead and enable them**。
4. 左侧选择 **Build evergo ReSukiSU Kernel** → **Run workflow** → 选 `main` 分支 → 点绿色按钮。
5. 等待约 20~40 分钟，构建完成后在该次运行页面底部的 **Artifacts** 下载 `evergo-resukisu-kernel`，解压得到 `evergo-resukisu-日期.zip`。

## 刷入（需要已解锁 BL）

1. **先备份当前 boot 分区**（重要！方便回滚）：在已 root 的环境或 TWRP 中执行
   `dd if=/dev/block/by-name/boot of=/sdcard/boot_backup.img`
2. 把 zip 传到手机，在 **TWRP / OrangeFox** 中直接刷入；或者用 kernelSU/Magisk 的方式替换 boot.img 后 `fastboot flash boot boot.img`。
3. 开机后安装 **ReSukiSU 管理器 APK**（从 https://github.com/ReSukiSU/ReSukiSU 的 Releases 下载），打开显示内核版本即成功。

## 注意事项

- 本内核基于 4.14.336 源码（你当前是 4.14.186 原厂内核），evergo 社区已有大量在 MIUI/HyperOS 上刷此源码内核的先例，但**刷机有风险，若不开机请用 MiFlash 线刷官方完整包恢复**。
- 未集成 SUSFS（官方已放弃 Non-GKI 支持）。若后续需要，需自行从 susfs4ksu 的 `gki-android12-5.10` 分支移植补丁，并改用 manual hook 模式。
- 如需改用自己 fork 的内核源码，修改 `.github/workflows/build-resukisu.yml` 顶部的 `KERNEL_REPO` / `KERNEL_BRANCH` 环境变量即可。
- Actions 免费额度（Public 仓库无限时）足够，单次构建约 30 分钟。
