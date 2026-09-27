# mimic-results

人形机器人动作跟踪的复现和移植记录。这里先放一部分成果，代码还在整理，之后会传上来。

## 做了些什么

先在 Unitree G1 上把 BeyondMimic 的动作跟踪部分跑通了，训了走路、冲刺、搏击三段动作。

然后把整套流程搬到了智元 X2 上。X2 没有现成可用的动作数据，所以从人体动捕数据开始，自己做了一遍重定向。这一步踩的坑最多：两台机器人的关节名字一样，但零点和方向不一样，直接套用 G1 的数据姿态是错的。重定向配置里也有几处角度偏差，要一处一处找出来修掉。

训好的 X2 策略又放到 MuJoCo 里做了 sim2sim，能稳定走完。换个错的输入它就会摔，说明这个检验是有效的。

最后比了一下 X2 为什么跟得没有 G1 好。结论是大头出在机器人本身，重定向只占一小部分。关节速度上限也排除掉了，剩下的原因还在查。

## 流程

```
人体动捕 (.bvh)
  → 重定向到机器人关节角
  → 转成训练要的格式，检查越限和离地
  → 正运动学 + 重采样
  → PPO 训练（Isaac Lab）
  → 导出 ONNX，在 MuJoCo 里验证
```

## 效果

Isaac Lab 里的回放。

**Unitree G1**

| 走路 | 冲刺 | 搏击 |
|---|---|---|
| ![G1 走路](media/g1_walk1.gif) | ![G1 冲刺](media/g1_sprint1.gif) | ![G1 搏击](media/g1_fight1.gif) |

**智元 X2**

| 走路 | 搏击 |
|---|---|
| ![X2 走路](media/x2_walk1.gif) | ![X2 搏击](media/x2_fight1.gif) |

MuJoCo 的视频之后再补。

## 用到的开源项目

[BeyondMimic](https://github.com/HybridRobotics/whole_body_tracking)、[GMR](https://github.com/YanjieZe/GMR)、[Isaac Lab](https://github.com/isaac-sim/IsaacLab)、[MuJoCo](https://github.com/google-deepmind/mujoco)

## 关于数据

动作数据来自 Ubisoft 的 [LAFAN1](https://github.com/ubisoft/ubisoft-laforge-animation-dataset)，许可证是 CC BY-NC-ND 4.0，不允许分发改编后的版本。所以重定向后的数据和训出来的策略文件都不放在这里。
