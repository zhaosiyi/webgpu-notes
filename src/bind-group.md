# 绑定组 Bind Group

WebGPU 中 vertex buffer 和 index buffer 等具有固定作用的缓冲区会具有专门的数据传输接口。

而 uniform buffer，texture 和 sampler 等其他自由度较高的数据则统一通过绑定组进行批量传输。
