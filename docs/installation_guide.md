---
title: 安装手册
app: 微重力测网设计系统 (MicroGravNet) v1.0.1
---

![banner](../resources/banner.png)

## 系统要求

| 组件 | 最低版本 | 说明 |
|------|----------|------|
| MATLAB | R2020b+ | 需要 App Designer、polyshape、struct |
| MATLAB Compiler | R2020b+ | 编译独立 .exe 时需要（可选） |
| Golden Software Surfer | 13+ | Tab 2 Kriging 插值需要（可选） |
| 操作系统 | Windows 10/11 | 依赖 COM Automation（Surfer） |

## 文件结构

```
微重力测网设计/
├── main.m                            ← 标准入口（推荐）
├── SurveyNetworkDesigner.m           ← App 类定义
├── README.md
├── lib/                              ← 核心算法库
│   ├── boundary/  (3 files)          ← 边界扩展/矩形检测
│   ├── design/    (7 files)          ← 测网设计内核+网格生成
│   ├── forward/   (7 files)          ← 重力正演
│   ├── io/        (5 files)          ← Surfer GRD/ASC 读写
│   └── kriging/   (2 files)          ← Kriging+误差分析
├── deploy/                           ← 编译部署
│   ├── compile.m                     ← mcc 编译脚本
│   ├── app_icon.png                  ← 应用图标
│   └── splash_screen.png            ← 启动画面
├── docs/                             ← 文档
├── test_data/                        ← 测试数据
├── resources/                        ← 图标/图幅资源
└── screenshots/                      ← UI 截图
```

## 安装步骤

### 1. 复制文件

将 `微重力测网设计/` 整个文件夹放置于本地磁盘。建议路径不含中文字符。

### 2. 配置授权系统

本系统复用通用授权系统（`E:\Claude_Code\验证授权系统`）。

- 确认 `E:\Claude_Code\验证授权系统\LicenseManager.m` 存在
- 首次运行任意已授权 App 会自动生成 `%APPDATA%\AppLicenseSystem\License.key`
- 若已激活过套件内任一 App（如二维数据处理），本系统直接免激活通过

**手动激活**：若为全新机器，启动 App 时自动弹出授权对话框，粘贴 `License.key` 文件内容即可。

**部署时**：将 `LicenseManager.m` 复制到 `deploy/` 目录，随 App 一同编译。

### 3. 安装 Surfer（可选）

Tab 2 的 Kriging 插值需要 Golden Software Surfer：

1. 安装 Surfer 13 或更高版本
2. 确保 Surfer 注册了 COM 组件（安装时默认注册）
3. 若 Surfer 安装在非默认路径，可在 Tab 2 中手动指定

无 Surfer 时，Tab 1/3/4 功能不受影响；Tab 2 仅可使用理论场计算功能。

### 4. 添加路径

在 MATLAB 命令窗口：

```matlab
addpath(genpath('E:\Claude_Code\微重力测网设计'));
savepath;
```

或直接运行 `main` 自动添加。

## 启动

**方式一（推荐）**：
```matlab
main
```

**方式二**：
```matlab
SurveyNetworkDesigner
```

## 编译为独立可执行文件

```matlab
cd E:\Claude_Code\微重力测网设计\deploy
compile
```

编译脚本自动调用 `mcc` 生成 `SurveyNetworkDesigner.exe`。需预先安装 MATLAB Compiler 工具箱。

## 验证安装

1. 启动 App，确认主窗口正常显示 4 个 Tab
2. Tab 3 → 「加载边界文件」→ 选择 `test_data/01_规则矩形_2000x1500.dat`
3. 填扩边距离 `1000` → 「计算扩展边界」→ 确认右侧显示边界扩展图
4. Tab 4 → 选择「混合测网」→ 目标区点距 `50`，最大埋深 `500` → 「生成测网」
5. 确认显示测网设计图且底部状态栏显示总点数

若以上步骤通过，安装成功。

## 常见问题

**Q: 启动报错 "LicenseManager not found"**
A: 检查 `E:\Claude_Code\验证授权系统\LicenseManager.m` 是否存在，或修改 `SurveyNetworkDesigner.m` 中的 `AuthPath` 属性。

**Q: Tab 2 Kriging 报错 "无法启动 Surfer"**
A: 确认 Surfer 已安装；在 Tab 2 中手动指定 Surfer.exe 路径；或运行 `actxserver('Surfer.Application')` 测试 COM 注册。

**Q: 混合测网角部突出**
A: 当前版本已修复：最外分级环带自动采用圆滑边界（`polybuffer`），与渐变区平滑过渡。

**Q: 导出 Excel 失败**
A: 确认有写入权限；检查磁盘空间。

**Q: 编译后运行报错**
A: 确保 `deploy/compile.m` 中列出的所有源文件均已复制到 `deploy/` 目录；检查 `LicenseManager.m` 是否已包含。
