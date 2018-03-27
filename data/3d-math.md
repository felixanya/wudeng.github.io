
计算机图形学第一准则：近似原则
    如果它看上去是对的它就是对的。

## 坐标系
两种不同的3D坐标系：左手坐标系，右手坐标系
    拇指x右方，食指y上方，其余三指z前方
    没有好坏之分
    传统计算机图形学使用左手坐标系, Unity3D中使用的就是左手坐标系。
    线性代数倾向于使用右手坐标系


Unity3D中
    世界坐标系World
        左手坐标
        transform.position
    屏幕坐标系Sceen
        左下(0,0)，右上(Screen.width, Screen.height)
        Screen.width = Camera.pixelWidth
        Screen.height = Camera.pixelHeight
        Input.mousePosition
        WorldToScreenPoint: 世界坐标转换为屏幕坐标
    视口坐标系Viewport
        标准化后的屏幕坐标
        (0, 0) -> (1, 1)
        WorldToViewportPoint

## 多坐标系
在不同的场合下使用不同的坐标系。
- 世界坐标系 World Space 东西南北
- 物体坐标系 Object Space 模型坐标系 上下左右
- 摄像机坐标系 Camara Space 摄像机在原点，x右，z前，y上，通过投影转换到2D屏幕
- 惯性坐标系 Inertial Space 远点与物体坐标系远点重合，但惯性坐标系的轴平行于世界坐标系的轴，物体坐标系转换到惯性坐标系只需要旋转，从惯性坐标系到世界坐标系只需要平移。

坐标变换


## 向量

### 点积 dot product

对应分量乘积的和，其结果为一个标量。
两个向量相似的程度，结果越大，越相似。

![](http://chart.googleapis.com/chart?cht=tx&chl=\vec{u}\cdot%20\vec{v}=|\vec{u}|\cdot%20|\vec{v}|\cdot%20cos(\theta))

- `>0`, 锐角;
- `=0`, 正交;
- `<0`, 钝角

用途：
- 判断两个矢量的角度，是相同，还是相反，是前方还是后方（如点到直线的投影是否在线段上），是顺时针还是逆时针（要用到垂直法矢量）。
- 求点到直线或者点到平面的距离：用直线或者平面上的任意一点，跟点连接成矢量，求该矢量于直线或者平面的法矢量的点积的绝对值。


### 叉乘 cross product
结果为向量，且不满足交换律
垂直于原来的两个向量
仅可应用于3D向

![](http://chart.googleapis.com/chart?cht=tx&chl=\vec{u}\cdot%20\vec{v}=|\vec{u}|\cdot%20|\vec{v}|\cdot%20sin(\theta))

用途：
- 求平面的法矢量

## 矩阵
    矩阵乘法
        不满足交换律
        满足结合律
        (A*B)T = AT * BT
        行向量：
            vABC
            DirectX使用行向量
        列向量
            CBAv
            OpenGL使用列向量
    方阵：线性变换

## 矩阵和线性变换

变换物体，物体上的点变化了，在同一坐标系描述变换前后点的位置。

变换坐标系，物体上的点并没有移动，只是在不同坐标系中描述他的位置。

两种变换在某种意义上是等价的，将物理变换一个量等价于将坐标系变换一个相反的量。

当有多个变换时，需要以相反的顺序变换相反的量。

### 旋转
旋转正方向：左手坐标系左手法则，右手坐标系右手法则。

2D中绕远点旋转，
3D中绕向量n旋转，n是基向量时比较简单，如果是n是任意轴，将向量分解成平行向量和垂直向量，平行向量不变，垂直向量与n求叉积得到第三个垂直向量。

### 缩放

### 变换分类
- 线性变换：F(a + b) = F(a) + F(b), F(ka) = kF(a)
- 仿射变换：线性变换后平移，线性变换的超集，v' = vM + b
- 可逆变换：基本变换除了投影都是可逆的
- 等角变换
- 正交变换
- 刚体变换

## 齐次坐标 Homogeneous coordinates, Projective coordinates

n维的向量用一个n+1维向量来表示。点的w分量设为1，方向的w分量设为0，方向要忽略平移效果。


合并矩阵运算中的乘法和加法。

笛卡尔坐标的平移和透视投影不能表示成矩阵相乘。

Affine Transform 引入额外的一维，一个矩阵搞定平移。
- 左上的3X3矩阵代表旋转及缩放
- 下面的1X3平移矢量
- 右边3X1零矢量
- 右下角标量1
最右列必然是[0,0,0,1]的转置。

Affine transformation 仿射变换
- 平移 Translation
- 缩放 Scale
- 翻转 Flip
- 旋转 Rotation
- 错切 Shear


## 方位orientation与角位移
位置：相对于参考点的位移
方位：从惯性坐标系到物体坐标系的角位移。相对已知方位的旋转，旋转的量称为角位移。
方位和角位移的区别就像点和向量的区别一样，数学上等价，该你上不同。第一个描述一种状态，第二个描述两个状态的差别。
矩阵和四元数表示角位移，用欧拉角表示方位。

- 方向 direction
- 角位移
- 旋转



描述方法：
- 矩阵
- 欧拉角 Euler Angles 万向锁问题
  heading-pitch-bank,
  - heading: y轴旋转，此时物体坐标系和惯性坐标系的y轴重合。
  - pitch：物体坐标系的x轴
  - bank: 物体坐标系的z轴
  roll-pitch-yaw
- 四元数 Quaternion
  - 轴角定义旋转 `(\vec{n}, \theta)` 绕`\vec{n}`指定的轴旋转`\theta`角
  - `q = [cos(\theta / 2), sin(\theta / 2)\vec{n}]`
  - q 与 -q 代表的角位移相同
  - 单位四元数 `[1, \vec{0}]`
  - `[w, \vec{v}]` 的共轭 `[w, -\vec{v}]`
  - 叉乘满足结合律，不满足交换律
  - 平滑插值，slerp, squad
  - 快速连接和角位移求逆
  - 能与矩阵形式快速转换
