---
title: 少儿编程——Python如何将一张输入图片改成素描风
tags:
  - 小技巧
  - python
excerpt: |-
  昨天去妈妈的一个朋友家里做客，看到阿姨的10岁的孩子在上编程课，感觉挺有意思的，就听了听，感觉很有意思
  题目
  题目就是要把一张正常输入的照片转换成素描风。
  步骤
  白话步骤

  将彩色图片转换为黑白图片
  将图片颜色反转
  将图片进行模糊处理
  将模糊后的图像颜色反...
date: 2024-02-04 20:49:00
categories:
---

昨天去妈妈的一个朋友家里做客，看到阿姨的10岁的孩子在上编程课，感觉挺有意思的，就听了听，感觉很有意思 题目 题目就是要把一张正常输入的照片转换成素描风。 步骤 白话步骤 将彩色图片转换为黑白图片 将图片颜色反转 将图片进行模糊处理 将模糊后的图像颜色反...
<!-- more -->
昨天去妈妈的一个朋友家里做客，看到阿姨的10岁的孩子在上编程课，感觉挺有意思的，就听了听，感觉很有意思

# 题目

题目就是要把一张正常输入的照片转换成素描风。

# 步骤

## 白话步骤

1.  将彩色图片转换为黑白图片
2.  将图片颜色反转
3.  将图片进行模糊处理
4.  将模糊后的图像颜色反转
5.  进行颜色减淡操作

## 步骤

1.  将原图色域空间转换为"L"
2.  反转图片颜色
3.  对图片应用高斯模糊
4.  将高斯模糊后的图片再次反转
5.  进行颜色减淡操作

# 代码

```python
from PIL import Image, ImageOps, ImageFilter

# 读取并转换图像
original_image = Image.open("E:\\images\\10.jpeg")
gray_image = original_image.convert("L")

# 反转并模糊图像
inverted_image = ImageOps.invert(gray_image)
blurred_image = inverted_image.filter(ImageFilter.GaussianBlur(8))  # 8是模糊半径

# 获取图像数据
gray_pixels = gray_image.load()
blurred_pixels = blurred_image.load()

# 创建一个新的图像用于输出
width, height = gray_image.size
sketch_image = Image.new("L", (width, height))
sketch_pixels = sketch_image.load()

# 使用循环处理每个像素
for x in range(width):
    for y in range(height):
        # 获取原始灰度图像和模糊图像的像素值
        base_pixel = gray_pixels[x, y]
        blend_pixel = blurred_pixels[x, y]

        # 应用颜色减淡效果
        if blend_pixel == 255:
            sketch_pixels[x, y] = 255
        else:
            sketch_pixels[x, y] = min(255, int(base_pixel + base_pixel * blend_pixel / 255))

# 显示和保存图像
sketch_image.show()
sketch_image.save("E:\\images\\10_sketch.jpg")
```

> 在铅笔画效果的颜色减淡算法中，当模糊图像的像素为255时，按照颜色减淡的公式，会导致除以零的情况（因为减淡公式中有一个 1 - Blend Color 的部分，当 Blend Color 为255时，这部分等于零）。为了避免这种除以零的错误，直接将结果像素值设为255，即最亮的白色，这样可以保证算法的稳定性和输出图像的正确性。

现在已经内卷到这种地步了吗，四年级的小朋友就已经开始写代码了，这也太可怕了吧😭😭😭