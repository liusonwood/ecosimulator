实现计划：在 EcoSim 中加入食肉动物与食草动物动态渲染

  背景与目标
  在现有 EcoSim 模拟器中加入食草动物（🐰）和食肉动物（🦊），实现其种群与 Lotka-Volterra 计算结果的精确对应，并通过独立的 Canvas
  图层进行渲染，不干扰现有 UI，保持视觉清晰（无残影）。

  关键变更说明
   1. 计算层 (main.js):
       * 在 EcoSimulator 逻辑中，增加 hareCount 和 lynxCount 的状态更新。
       * 增加 Agent 管理系统，根据种群数量动态实例化和销毁图标对象。
   2. 渲染层 (index.html):
       * 在主 Canvas 容器内添加一个新的透明 Canvas entityLayer。
       * 使用 pointer-events: none 确保不干扰 UI 事件。
   3. 渲染逻辑 (main.js):
       * 每个图标根据种群数量对应，使用简单位置插值确保平滑游动。
       * 硬清屏（clearRect），不保留任何残影。

  实施步骤

  1. 结构更新 (index.html)
   - 在 .chart-container 内增加 <canvas id="entityLayer" style="position: absolute; top: 0; left: 0; pointer-events: none;"></canvas>。
   - 确保容器相对定位（position: relative）。

  2. 逻辑扩展 (main.js)
   - 在 EcoSimulator 类中增加 AnimalAgent 类，存储 x, y, type。
   - 在 stepSimulation 中，根据 LV 模型输出的 H 和 L 更新 Agent 数组长度。
   - 新增 renderEntities 函数，在 requestAnimationFrame 中独立绘制。

  3. 修改chart部分使其显示食草动物（🐰白色）和食肉动物（🦊橙红色）图例和曲线

  4. 验证方案
   - 模拟开始后，屏幕上应出现对应数量的 🐰 和 🦊。
   - 种群波动时，图标数量应实时增减。
   - 交互（点击图例/扰动）应正常工作，且不会驱散动物。