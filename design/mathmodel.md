# 任务：实现空间显式植被群落动态模型 (Spatially Explicit Vegetation Model)

## 1. 模型背景与网格定义
- **网格系统**：建立一个 $L \times L$ 的二维 NumPy 矩阵（建议尺寸 $20 \times 13$）。每个格子存储 $N=5$ 个功能型物种（地衣、苔藓、草本、灌木、乔木）的当前生物量 $B_k$（盖度 0-1）和种子库 $S_k$。
- **初始状态设定 ($t=0$)**：
  - **植物盖度 $B$**：随机选择 10% 的格子，为地衣 ($k=1$) 赋予随机盖度 $B_1 \sim Uniform(0.05, 0.2)$。其他功能型 $(k=2,3,4,5)$ 初始盖度设为 0。
  - **种子库 $S$**：所有格子统一预置初始值 $S_k = [0.5, 0.2, 0.1, 0.02, 0.01]$，模拟孢子与种子的先验存在。
- **状态约束**：严格遵守物理约束 $\sum B_k \le 1$ 以及 $B, S \ge 0$。

## 2. 核心算法逻辑 (年度循环)
请按以下顺序使用向量化运算（Vectorized Operations）实现各模块：

### 0. 背景种子输入 (Background Seed Rain)
- **长距离传播模拟**：在每年扩散阶段前，为所有格子的种子库添加微小背景种子雨：$S_k(i,j,t) += \beta_k$。
- **输入参数 $\beta$**：地衣(0.2), 苔藓(0.1), 草本(0.05), 灌木(0.01), 乔木(0.005)。该设计确保系统在遭受严重扰动后仍能重启演替。

### A. 种子扩散与初次萌发 (Dispersal & Primary Germination)
- **产量计算**：$P_k = B_k \times \rho_k$（单位盖度年产种子数）。
- **空间扩散**：利用高斯传播核（Gaussian Kernel）进行卷积运算。传播核标准差由参数 $\lambda_k$ 决定，且必须在 $R_{max} \ge 3\lambda$ 处截断。
- **萌发分配**：
  - 计算有效萌发率：$g_{eff} = g_k \times (1 - \sum B_{total})$。
  - 新增生物量：$\Delta B_{germ} = \min(g_{eff} \cdot R_k, 1 - \sum B_{total})$。
  - 种子库入库：未萌发的种子 $(1 - g_{eff}) \cdot R_k$ 累加至 $S_k$。

### B. 本地竞争 (Competition - Lotka-Volterra)
- **增长方程**：
  $$B_{new, k} = B_k + r_k B_k \left(1 - \frac{\sum_{l=1}^{N} \alpha_{kl} B_l}{K_{local, k}}\right)$$
- **环境修正**：$K_{local, k}$ 为基础承载力 $K_k$ 经由土壤厚度乘子修正后的值。
- **归一化处理**：若 $\sum B_{new} > 1$，需按比例压缩各物种生物量，使总和等于 1。

### C. 种子库动态 (Seed Bank Dynamics)
- **自然损耗**：$S_k = S_k \times s_k$。
- **二次补位萌发**：在竞争结束后的空闲空间中，种子库按比例 $\nu_k$ 尝试再次萌发：
  $\Delta B_{bank} = \min(\nu_k \cdot S_k, 1 - \sum B_{next})$。同步更新 $B$ 和 $S$。

### D. 交互式扰动接口 (Manual Disturbance)
- **触发扰动机制**：由用户通过界面（如点击或按钮）手动触发。
- 提供接口 `apply_disturbance(x, y, type)`：生成圆形 Mask，根据扰动类型对应的致死率削减范围内物种的 $B$ 和 $S$。由于背景种子持续输入，演替过程将在扰动后自动恢复。

## 3. 技术栈与输出
- **后端**：Python (NumPy, SciPy)。
- **可视化**：使用 Matplotlib 动态热力图渲染展示各物种分布演替过程。

## 4. 参数表格
文件夹中有一份包含模型参数定义的 CSV 文件（parameters-2.0.csv）：
1. **物种生物学参数**：$r, \rho, \lambda, g, s, \nu, K$。
2. **$N \times N$ 竞争矩阵**：$\alpha_{kl}$。