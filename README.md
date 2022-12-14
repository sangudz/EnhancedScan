# 增强扫描pdf书籍类文档

## 第一阶段：python-cv2 二值化初步实现

一些带图片的书或文档都不会采取强力的黑白二值增强扫描，灰黑的字体，配上白灰有些脏的背景，这导致阅读体验十分的不佳。

因此，我就萌生了做一个更加干净的增强扫描的想法。也就是，文档的字体是纯黑，背景是纯白。

这样的二值操作并非一件难事，大可设置一个阈值，高于则替换为255纯白，低于则0。这样整个文档就只有黑白，而没有灰色了。

那么，很容易只需要

```python
    ret, binary = cv2.threshold(img, 240, 255, cv2.THRESH_BINARY) # 阈值 240
```

然而，这样的阈值不尽人意，很多字体之外的灰色噪点也变成了黑色，整个页面脏的吓人。

那么我们将阈值调低一些呢？220 页面不那么脏了，然而字体却开始缺胳膊少腿。

根据不同的文档调调阈值这样的初步实现还是基本能用的。

接下来，读取pdf中的img和保存增强的图片到pdf的操作都是由pymupdf完成的，没啥好说的。

## 第二阶段：Otsu's 二值处理

如下图所示，我们之前的处理就是最为朴素的global方式。此外，还有用到 cv.THRESH_OTSU 实现的adaptive mean。

cv.THRESH_OTSU原理：。。。

为了消除背景的噪点，我们还需要在二值化之前，进行高斯模糊cv.GaussianBlur，得到adaptive gaussian。
 
![ada_threshold](https://user-images.githubusercontent.com/113080365/189276094-c7fe3f38-7927-4d44-bd7e-c97e2ba3851b.jpg)

adaptive gaussian 的代码如下：
```python
    blur = cv2.GaussianBlur(enhan,(3,3),0)
    ret, binary = cv2.threshold(blur, 0, 255, cv2.THRESH_OTSU+cv2.THRESH_BINARY)
```

详细参考链接；https://docs.opencv.org/4.x/d7/d4d/tutorial_py_thresholding.html


![unnamed](https://user-images.githubusercontent.com/113080365/189028607-854b494e-f543-4e1e-8d54-4bf054684fe8.png)

![unnamed](https://user-images.githubusercontent.com/113080365/189027803-71401fd1-27c4-4b73-93f0-464cf07edf71.png)
