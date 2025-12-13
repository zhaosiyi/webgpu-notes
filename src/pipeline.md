# 渲染管线 Pipeline

渲染管线用于描述 GPU 的工作流程。

一个完整的 pipeline 工作流程通常会包含以下内容：

- 输入数据准备：包括缓冲区，纹理，采样器等。
- 顶点处理阶段：设置顶点着色器的入口函数，输入缓冲区等
- 光栅化阶段：可进行图元装配，裁剪等操作
- 片元处理阶段：设置片元着色器入口函数，输入缓冲区等，还有混合、深度测试等也在此进行
- 输出结果：将渲染结果输出到帧缓冲区

常用的 Pipeline 配置：

```ts
this.pipeline = device.createRenderPipeline({
  layout: 'auto', // 通常固定 auto 即可
  // 顶点处理阶段配置
  vertex: {
    module: shaderModule, // 着色器代码模块
    entryPoint: 'vertexMain', // 着色器入口函数
    buffers: [
      // 着色器入口函数的输入参数（即缓冲区）数据格式
      {
        arrayStride: 16, // 每个顶点数据的字节数
        attributes: [
          // 单个顶点数据的格式
          { shaderLocation: 0, offset: 0, format: 'float32x2' },
          { shaderLocation: 1, offset: 8, format: 'float32x2' },
        ],
      },
    ],
  },
  // 片元处理阶段配置
  fragment: {
    module: shaderModule,
    entryPoint: 'fragmentMain',
    targets: [{ format: gpu.getPreferredCanvasFormat() }],
  },
  // 光栅化配置
  primitive: {
    topology: 'triangle-list', // 根据给定的索引缓冲区绘制片元
  },
});
```

除了使用 `createRenderPipeline()` 创建渲染管线外，还可以使用 `createComputePipeline()` 创建计算管线。
