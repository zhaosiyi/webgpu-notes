# WGSL

## 数据类型 

WGSL 中常用的基本数据类型：

- `bool`
- `u32`
- `i32`
- `f32`
- `f16`



## 变量声明

使用 `var` 声明变量，使用 `let` 声明常量：

```wgsl
var a: u32 = 2;
a = 1;

let b: f32 = 2.0;
```

## 函数定义

使用 `fn` 关键字定义函数：

```wgsl
fn add(x: f32, y: f32) -> f32 {
  return x + y;
}
```

## 流程控制语句

WGSL 支持 `if-else` 和 `for` 语句，写法和 JavaScript 差不多：

```wgsl
for (var i:u32 = 0; i < n; i++) {
  s += 0.05;         
}

if (a > 1) {
  ...
} else {
  ...
}
```

## 类型转换

WGSL 是强类型，不同类型的数值之间无法直接运算：

```wgsl
let a = 1;     // a 是 i32 类型
let b = 2.0;   // b 是 f32 类型
let c = a + b; // 错误：不能将一个 i32 的值添加到 f32 的值上
```

可以用和类型名称相同的函数进行显式类型转换：

```wgsl
let a = 1;     // a 是 i32 类型
let b = 2.0;   // b 是 f32 类型
let c = f32(a) + b; // 这样就行了
```

可以在编写数值字面量时添加后缀标注其类型：

```wgsl
2i   // i32
3u   // u32
4f   // f32
4.5f // f32
5h   // f16
5.6h // f16
6    // 抽象整数
7.0  // 抽象浮点数
```

## 数组

WGSL 中使用 `array<类型, 长度>` 声明数组：

```ts
let a = array<f32, 5>;   // 一个包含五个 f32 的数组
let b = array<vec4f, 6>; // 一个包含六个 vec4f 的数组
```

### 运行时大小数组

在根作用域声明的数组可以不指定大小，其实际尺寸将根据绑定组动态变化：

```wgsl
struct Stuff {
  color: vec4f,
  size: f32,
  verts: array<vec3f>,
};

@group(0) @binding(0) var<storage> foo: array<mat4x4f>;
@group(0) @binding(1) var<storage> bar: Stuff;
```

在使用时通过 `arryLength()` 函数获取其大小：

```wgsl
let numMatrices = arrayLength(&foo);
let numVerts = arrayLength(&bar.verts);
```

> [!NOTE]
> 动态数组并不常用，很多时候我们都会选择直接开一个较大的数组，并在 uniform 缓冲区里存放数组长度。

## 向量

### 向量的类型

WGSL 提供三种向量类型：

- `vec2<T>`：2维向量，常用于表示 2D 坐标
- `vec3<T>`：3维向量，常用于表示 3D 坐标，或齐次的 2D 坐标
- `vec4<T>`：4维向量，常用于表示颜色，或齐次的 3D 坐标

为了简化类型模板参数的编写，WGSL 还提供了一些类型别名：

- `vec4f`：等价于 `vec4<f32>`

### 向量初始化

初始化向量：

```wgsl
let a = vec4<f32>(1,2,3,4);
```

构造参数中也可以传入向量，它们将被自动展开：

```wgsl
let a = vec2f(1, 2);
let b = vec3f(0, a); // 等价于 vec3f(0, a[0], a[1])

// cdef 向量都是和 a 相等的
let a = vec4f(1, 2, 3, 4);
let b = vec2f(2, 3);
let c = vec4f(1, b, 4);
let d = vec4f(1, a.yz, 4);
let e = vec4f(a.xyz, 4);
let f = vec4f(1, a.yzw);
```

### 访问向量元素

我们可以直接通过下标访问元素，也可以通过 `x,y,z,w` 和 `r,g,b,a` 几个别名来访问对应下标元素：

```wgsl
// 这些写法都表示访问向量的第一个元素
a[0], a.x, a.r
```

也可以通过 2 或 3 个别名字母的自由组合访问多个元素，以向量的形式返回：

```wgsl
a.xy // 返回 vec2<f32>(a.x, a.y)
a.xyz // 返回 vec3<f32>(a.x, a.y, a.z)
a.zzy // 重复访问也是可以的，相当于 vec3<f32>(a.z, a.z, a.y) 
```

### 向量的运算符

加减乘除都是可以直接作用在两个同类型向量上的，相当于对相同下标的元素分别计算结果：

```wgsl
let a = vec4f(1, 2, 3, 4);
let b = vec4f(5, 6, 7, 8);
let c = a + b;  // c 是 vec4f(6, 8, 10, 12)
let d = a * b;  // d 是 vec4f(5, 12, 21, 32)
let e = a - b;  // e 是 vec4f(-4, -4, -4, -4)
```

## 矩阵

WGSL 中有许多类型的矩阵，其类型名称的格式均为 `mat<向量数量>x<向量大小><元素类型>`：

- `mat3x4f`：表示包含 3 个 `vec4f` 的数组，也可以写成 `mat3x4<f32>`

用下标访问矩阵将得到一个向量：

```wgsl
let a = mat4x4f(....);
let b = a[2];  // b 是 a 中第3个向量的一个 vec4f
```

可以直接使用 `*` 计算矩阵与向量/矩阵的乘法：

```wgsl
let a = mat4x4f(....);
let b = vec4f(1, 2, 3, 4);
let c = a * b;  // c 是一个 vec4f，是 a * b 的结果
```
