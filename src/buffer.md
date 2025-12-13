# 缓冲区

GPU 中无法直接访问内存数据，需要先把 GPU 需要用到的数据保存到缓冲区中传递给 GPU 使用。

## 顶点缓冲区 Vertex Buffer

保存顶点着色器输入数据的缓冲区。

创建顶点缓冲区：

```ts
const vertexBuffer = device.createBuffer({
  size: vertices.length * 4,
  usage: GPUBufferUsage.VERTEX | GPUBufferUsage.COPY_DST,
});
```

> [!NOTE]
> 尽管在当前 chrome 实现中，即使不添加 `GPUBufferUsage.COPY_DST` 也可以通过 `device.queue.writeBuffer()` 等方式向缓冲区写入数据。\
> 但根据 WebGPU 规范，只要我们想往缓冲区中写入数据，就应当标记 COPY_DST 标志位，这样可以明确语义。

### 设置缓冲区数据

```ts
const data = Float32Array.from(xxx);

device.queue.writeBuffer(vertexBuffer, 0, data);
```

还有另一种相对麻烦一些的写法，但初始化性能更高，会减少临时副本的创建开销：

```ts
this.vertexBuffer = device.createBuffer({
  size: vertices.length * 4,
  usage: GPUBufferUsage.VERTEX | GPUBufferUsage.COPY_DST,
  mappedAtCreation: true, // 初始为缓冲区创建内存映射
});

new Float32Array(this.vertexBuffer.getMappedRange()).set(vertices); // 向缓冲区写入数据
this.vertexBuffer.unmap(); // 关闭缓冲区的内存映射
```

通常建议动态的缓冲区数据使用第一种写法，固定内容的缓冲区使用第二种写法。

### 顶点缓冲区的数据格式

在渲染管线中配置顶点着色器的输入数据时，可定义顶点缓冲区的数据格式：

```ts
this.pipeline = device.createRenderPipeline({
  // ...省略无关配置
  vertex: {
    module: shaderModule,
    entryPoint: 'vertexMain',
    buffers: [ // 在这里设置输入给着色器函数的缓冲区数据格式
      {
        arrayStride: 16, // 每个顶点数据占据 16 字节
        attributes: [
          // 单个顶点数据的格式，在 WGSL 中可通过 location(0) 和 location(1) 访问到对应数据
          { shaderLocation: 0, offset: 0, format: 'float32x2' }, // float32x2 对应 WGSL 中的 vec2f 类型
          { shaderLocation: 1, offset: 8, format: 'float32x2' },
        ],
      },
    ],
  },
});
```

## 索引缓冲区 Index Buffer

保存片元着色器输入数据的缓冲区，通常其中会保存顶点缓冲区中的数据下标。

片元着色器一般以三个顶点一组的顺序绘制顶点数据构成的三角形。

GPU 默认会按照顶点缓冲区中数据的顺序绘制三角形，当我们需要自定义怎样使用顶点构造三角形时，就需要设置索引缓冲区。

## uniform 缓冲区

用于向着色器函数传递自定义常量数据，比如矩阵、颜色等。

单次 GPU 渲染期间，uniform 缓冲区中的数据无法改变。

但在不同帧之间可以使用不同的 uniform 数据。
