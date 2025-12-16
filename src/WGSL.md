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
fn add(x: f32, y: f32) {
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

## 向量

WGSL 中常用四维向量表示颜色：

```wgsl
var color = vec4<f32>(1.0, 0.0, 0.0, 1.0);
```
