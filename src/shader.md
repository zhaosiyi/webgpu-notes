# 着色器 Shader

在 GPU 中运行的函数被称为 **着色器（shader）** 。WebGPU 的着色器代码均由 **WGSL** 语言编写。

目前 WebGPU 存在三种类型的着色器：

- **顶点着色器（vertex shader）**：负责将原始的顶点数据转化为 canvas 坐标。
- **片元着色器（fragment shader）**：负责以三角形为单位绘制顶点构成的面区域。
- **计算着色器（compute shader）**：负责在 GPU 中执行其他计算任务。

> [!NOTE]
> WebGPU 中的画布坐标格式与 canvas 2D 中有所不同：
> - canvas 2D 中画布原点位于画布 **左上角**，x 轴正方向向右，y 轴正方向 **向下**，使用 **绝对值坐标** 表示。
> - WebGPU 中画布原点位于画布 **正中心**，x 轴正方向向右，y 轴正方向 **向上**，使用 [-1, 1] 的 **归一化坐标** 表示。

## 加载着色器代码

在 WebGPU 中，会这样加载着色器代码：

```ts
const shaderModule = device.createShaderModule({
  code: shaderCode,
});
```

后续在创建渲染管线（pipeline）时，会需要配置使用哪个模块的着色器代码。
