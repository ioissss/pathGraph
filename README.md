# pathGraph
路径图，复用当前帧的已追踪的所有交点的radiance值，一个局限于当前帧的样本复用方法。

# IDEA
渲染每一帧时，追踪了 W*H根光线(1SPP)，我们实际上只利用了第一跳交点的出射radiance。实际上，我们可以记录所有光线每一跳的入射radiance，以及直接光照信息(来源于NEE),通过空间复用来重用某个交点附近的其他交点的radiance，从而提高样本的利用率。

具体可以参考论文 "Path Graphs: Iterative Path Space Filtering"。论文没有公开源码，这个工作是对这个方法的一个复现。

# 效果
测试场景 - classroom

![image](https://github.com/user-attachments/assets/f8a5c2f3-a83a-4ffc-bc64-e8bb45ff782c)

---

## 对比：质量

| 1SPP,MAX_DEPTH = 6 | 路径图迭代10轮 |
| --- | --- |
| ![原图](https://github.com/user-attachments/assets/a6c2dade-a799-4f24-bb88-607dd22f1216) | ![迭代后](https://github.com/user-attachments/assets/e2d957c2-fd2f-4de8-8aaf-198101ba20e9) |

**图注：**
- **左图**：1spp，max_depth = 6的渲染结果。
- **右图**：左图基础上，10轮Path Graph迭代的结果。

迭代一定数量后，重建的图像质量好了很多，再配合降噪手段可以取得不错的效果。不过收集的路径信息或许也可以用来专门设计一个神经网络降噪器，效果应该会更好。
# 优缺点分析
优点：通过空间领域复用，充分榨干路径样本信息，可以将图像质量提升一个level

缺点：简单地根据位置距离和法线距离复用样本容易出问题，不过不容易看出来。虽然可以提升图像质量，不过还是需要配合降噪手段才行，速度还是比较慢的，适用于离线渲染。
