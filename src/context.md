# GPU 上下文

和 canvas 2D 中类似，使用 WebGPU 也需要初始化 canvas 上下文，就是比在 2d 中要复杂一些：

```
// 获取 GPU 接口
const { gpu } = navigator;

// 获取 GPU 适配器
const adapter = await gpu.requestAdapter();

// 获取 GPU 设备接口
const device = (this.device = await adapter!.requestDevice());

// 获取 GPU 上下文
const ctx = (this.ctx = canvas.getContext('webgpu')!);

// 初始化 GPU 上下文
ctx.configure({
  device,
  format: gpu.getPreferredCanvasFormat(),
  alphaMode: 'premultiplied',
});
```

这里很少会用到自定义配置，几乎固定是这套代码。
