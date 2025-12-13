# 命令编码器 Command Encoder

Command Encoder 负责将 GPU 需要执行的命令打包到一起批量提交给 GPU，以提高程序性能。

## 创建 Command Encoder

```ts
const commandEncoder = this.device.createCommandEncoder();
```

## 渲染通道 Render Pass

Render Pass 表示单次渲染的完整流程。

通常它的代码结构如下：

```ts
const renderPass = commandEncoder.beginRenderPass({
  colorAttachments: [
    {
      view: ctx.getCurrentTexture().createView(), // 设置渲染目标位 canvas 上下文
      clearValue: { r: 0, g: 0, b: 0, a: 0 }, // 黑色背景
      loadOp: 'clear',    // 开始时清空之前的画面
      storeOp: 'store'    // 结束时保存渲染结果
    },
  ],
});

renderPass.setPipeline(this.pipeline);
renderPass.setVertexBuffer(0, this.vertexBuffer);
renderPass.setIndexBuffer(this.indexBuffer, 'uint32');
renderPass.setBindGroup(0, this.bindGroup);
renderPass.drawIndexed(this.indexCount);
renderPass.end();

this.device.queue.submit([commandEncoder.finish()]);
```
