# 光华门 · 粒子记忆

TouchDesigner 实时装置。同一座城门在三个时间断面上的「显影」：1937 年的完整城楼、战火中的破损墙体、今天不及人高的残墙与民国堡垒遗迹。三态由三份点云资产驱动，手势或键盘切换，点云在「消散—重组」中过渡。

## 文件

```
NewProject/NewProject.toe        工程主文件（TouchDesigner 2025.33230）
assets/pointclouds/
  GuanghuaGate_PointCloud_1000000.ply                              完整城门（原貌）
  Tripo_Visual_Rebuild_V04_1793461568726_r1n6_pc500k_color.ply     破损墙体（破损）
  Guanghua_Gate_Bastion_Ruins_V07_Particles_500000_WithNormals.ply 残墙与堡垒遗迹（遗址）
```

## 运行

1. 用 TouchDesigner **2025.33230** 或更高版本打开 `NewProject/NewProject.toe`。
2. 按 **F1** 进入 Perform Mode（Esc 退出）。画面输出为 `/project1/piece/out`（1280×720）。
3. 摄像头与手势：工程内置 Palette 的 MediaPipe 组件（`/project1/MediaPipe`，本地端口 63587）。首次运行需允许摄像头；没有手势时可用键盘（见下）演示。

> 点云使用的是绝对路径 `D:/光华门/assets/pointclouds/...`。换机器/换目录后，需在 `/project1/piece/geo` 与 `/project1/piece/geo_form` 下的 6 个 pointfilein 节点里改回新路径。

## 演示流程

| 阶段 | 时间 | 内容 |
| --- | --- | --- |
| 序幕 | 0.9s / 3.1s / 5.3s | 三行白字逐行淡入（1937年12月，光华门。/ 城墙高13米，护城河宽130米。/ 它挡住了日军，直到命令撤退。） |
| 提示 | 7.4s | 「伸手，进入」淡入 |
| 进入 | 手势 / 按键 / 32s 无操作自动 | 粒子铺满画面，角落提示：握拳 —— 原貌　比 2 —— 破损　张开 —— 遗址 |
| 三态 | 手势保持 ~1s 触发 | 握拳=原貌、比 2=破损、张开=遗址；过渡约 2.2s（消散后重组） |
| 结尾 | 三态都看过之后 | 三行结语依次淡入（1.6s / 3.0s / 4.4s），停留 10s，70s 后自动回到序幕 |

## 键盘（无手势时的演示通道）

| 键 | 作用 |
| --- | --- |
| `R` / 空格 | 从头重播（回到序幕） |
| `1` / `2` / `3` | 强制切到 原貌 / 破损 / 遗址（不依赖摄像头） |
| `0` / `Esc` | 取消强制，交回手势控制 |
| `F1` / `Esc` | 进入 / 退出 Perform Mode |

## 结构要点

- `/project1/piece/out` — 最终输出（nullTOP，1280×720）。
- `/project1/piece/ctrl/state` — 状态机（scriptCHOP），输出 `w0/w1/w2` 三态权重与文字透明度、`hint`、`amp` 等 24 个通道。
- `/project1/piece/geo`、`/project1/piece/geo_form` — 两套点云几何，各自 `blend`(blendPOP) 用 `ctrl/mix` 的 `w0/w1/w2` 按比例混合 intact / damaged / ruins 三份点云；`geo` 多一个 `noise` 用 `amp` 做尘埃抖动。
- `/project1/piece/psmat` — pointsprite 材质，点大小随三态权重变化；相机与色调也按权重插值。
- `/project1/piece/ctrl/key_restart` — keyboardinDAT，全局收键（不限于 Perform Window）。
