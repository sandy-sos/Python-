# 📊 Matplotlib 完全知识点总结

> 对应课程：莫烦Python Matplotlib 基础 + 扩展
> 2026-07-17 整理

---

## 目录

1. [基础核心](#一基础核心)
2. [图的构成与定制](#二图的构成与定制)
3. [子图布局](#三子图布局)
4. [常用图表类型](#四常用图表类型)
5. [进阶技巧](#五进阶技巧)
6. [新版知识点 (v3.6+)](#六新版知识点-v36)
7. [与我之前 OpenCV 练习的结合](#七与我之前-opencv-练习的结合)
8. [速查表](#八速查表)

---

## 一、基础核心

### 1.1 两种绘图风格

```python
import matplotlib.pyplot as plt

# 风格A：pyplot 方式（快速、适合交互）
plt.plot([1, 2, 3], [4, 5, 6])
plt.show()

# 风格B：面向对象方式（推荐、精细控制）
fig, ax = plt.subplots()
ax.plot([1, 2, 3], [4, 5, 6])
plt.show()
```

> **💡 莫烦课程重点：** 初学用 pyplot 方式好上手，但实际项目中面向对象方式更主流。

### 1.2 第一个完整图

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
y = np.sin(x)

fig, ax = plt.subplots(figsize=(8, 4))     # 创建画布
ax.plot(x, y, color='red', linewidth=2)     # 画折线
ax.set_title('正弦曲线 sin(x)')              # 标题
ax.set_xlabel('x 轴')                       # x标签
ax.set_ylabel('y 轴')                       # y标签
ax.grid(True, alpha=0.3)                    # 网格
plt.tight_layout()
plt.show()
```

---

## 二、图的构成与定制

### 2.1 图的层级结构

```
Figure（画布）
 └── Axes（坐标系 / 子图）
      ├── Title（标题）
      ├── XAxis / YAxis（坐标轴）
      │    ├── Tick Labels（刻度标签）
      │    └── Tick Lines（刻度线）
      ├── Spine（边框线：上下左右）
      ├── Legend（图例）
      ├── Grid（网格线）
      └── Artists（图形元素：Line2D, Patch, Text...）
```

### 2.2 颜色、线型、标记

```python
# 颜色
color='red'          # 名称
color='#ff0000'      # 十六进制
color=(1.0, 0, 0)    # RGB 元组 (0~1)
color='C0'           # 默认颜色循环 (C0~C9)

# 线型
linestyle='-'   # 实线  (solid)
linestyle='--'  # 虚线  (dashed)
linestyle='-.'  # 点划线(dashdot)
linestyle=':'   # 点线  (dotted)
linestyle=''    # 无线条

# 标记
marker='o'      # 圆点   marker='s'  # 方块
marker='^'      # 三角   marker='*'  # 星号
marker='.'      # 小点   marker='x'  # 叉
marker='D'      # 菱形   marker='v'  # 下三角

# 快捷组合
ax.plot(x, y, 'ro--')     # 红色圆点虚线
# 等价于 color='red', marker='o', linestyle='--'
```

### 2.3 坐标轴范围与刻度

```python
# 固定范围
ax.set_xlim(0, 10)
ax.set_ylim(-1.5, 1.5)

# 自动紧凑
ax.set_xlim(left=0)     # 只设左边界
ax.autoscale()          # 自动调整

# 自定义刻度
ax.set_xticks([0, 2, 4, 6, 8, 10])
ax.set_xticklabels(['零', '二', '四', '六', '八', '十'], fontsize=12)

# 刻度格式化（新版推荐）
ax.xaxis.set_major_formatter('{x:.1f}')   # v3.1+ 字符串格式化
ax.xaxis.set_major_locator(plt.MultipleLocator(2))  # 每隔2一个刻度

# 旋转刻度标签
plt.xticks(rotation=45)
```

### 2.4 文本与注释

```python
ax.text(x=5, y=0.5, s='文本标注', fontsize=12, ha='center')

# 带箭头标注
ax.annotate(
    '最高点', xy=(np.pi/2, 1), xytext=(np.pi/2+1, 1.2),
    arrowprops=dict(arrowstyle='->', color='red'),
    fontsize=12
)

# 图例（需要在plot时指定label）
ax.plot(x, y, label='sin(x)')
ax.plot(x, np.cos(x), label='cos(x)')
ax.legend(loc='upper right')        # 位置
# loc 可选: 'best', 'upper left', 'lower right', 'center' ...

# 数学公式（用LaTeX风格，注意加 r 原始字符串）
ax.set_title(r'$\sin(x)$ 与 $\cos(x)$')
ax.set_xlabel(r'$x$ (rad)')
```

### 2.5 字体与中文

```python
# 方案一：全局设置（推荐）
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']
plt.rcParams['axes.unicode_minus'] = False

# 方案二：局部设置
import matplotlib.font_manager as fm
font = fm.FontProperties(fname='C:/Windows/Fonts/simhei.ttf')
ax.set_title('中文标题', fontproperties=font)

# 方案三：样式表
plt.style.use('seaborn-v0_8-whitegrid')  # 新版 seaborn 名称
```

### 2.6 边框（Spine）控制

```python
# 隐藏上、右边框
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# 移动下边框（类似 y=0 轴）
ax.spines['bottom'].set_position(('data', 0))

# 改颜色
ax.spines['left'].set_color('red')
```

---

## 三、子图布局

### 3.1 均匀网格

```python
# 2×2 子图
fig, axes = plt.subplots(2, 2, figsize=(10, 8))
axes[0, 0].plot(x, y)
axes[0, 1].scatter(x, y, s=1)
axes[1, 0].hist(np.random.randn(1000), bins=30)
#hist 是 histogram（直方图） 的缩写，作用是统计数据分布。
axes[1, 1].bar(['A','B','C'], [3, 7, 2])
plt.tight_layout()
plt.show()

# 共享坐标轴
fig, (ax1, ax2) = plt.subplots(2, 1, sharex=True, figsize=(8, 6))
```

### 3.2 不规则布局

```python
# GridSpec — 精细控制
from matplotlib.gridspec import GridSpec

fig = plt.figure(figsize=(10, 6))
gs = GridSpec(3, 3, figure=fig)

ax1 = fig.add_subplot(gs[0, :])     # 第一行占全部宽
ax2 = fig.add_subplot(gs[1, :-1])   # 第二行左边两列
ax3 = fig.add_subplot(gs[1:, -1])   # 右边一整列
ax4 = fig.add_subplot(gs[2, 0])     # 第三行左下
ax5 = fig.add_subplot(gs[2, 1])     # 第三行中下
```

---

## 四、常用图表类型

### 4.1 折线图

```python
ax.plot(x, y, marker='o', markersize=4, label='数据A')
ax.fill_between(x, y-0.2, y+0.2, alpha=0.2)  # 填充区间
```

### 4.2 散点图

```python
n = 100
x, y = np.random.randn(2, n)
colors = np.random.rand(n)
sizes = np.random.rand(n) * 200

# c=颜色序列, s=大小序列, cmap=色图
ax.scatter(x, y, c=colors, s=sizes, cmap='viridis', alpha=0.6)
plt.colorbar(ax.collections[0])  # 颜色条
```

### 4.3 柱状图

```python
categories = ['A', 'B', 'C', 'D']
values = [3, 7, 2, 5]
errors = [0.5, 0.3, 0.4, 0.6]

ax.bar(categories, values, yerr=errors, capsize=5, 
       color=['#4f46e5', '#22c55e', '#f59e0b', '#ef4444'])

# 在柱子上显示数值（v3.4+）
ax.bar_label(ax.containers[0], fmt='%.1f')

# 分组柱状图
x = np.arange(3)
width = 0.35
ax.bar(x - width/2, [3, 5, 2], width, label='Group A')
ax.bar(x + width/2, [4, 3, 5], width, label='Group B')
ax.set_xticks(x)
ax.set_xticklabels(['猫', '狗', '鸟'])
```

### 4.4 水平柱状图

```python
ax.barh(categories, values)
ax.bar_label(ax.containers[0])   # 也支持水平
```

### 4.5 直方图

```python
data = np.random.randn(1000)
ax.hist(data, bins=30, density=True, alpha=0.7, 
        color='steelblue', edgecolor='white')
# density=True 归一化为概率密度
# 也可以叠加多条
ax.hist(data2, bins=30, density=True, alpha=0.5, color='orange')
```

### 4.6 箱线图

```python
data = [np.random.randn(100) for _ in range(5)]
ax.boxplot(data, labels=['A','B','C','D','E'],
           patch_artist=True,  # 填充颜色
           showmeans=True)     # 显示均值
```

### 4.7 饼图

```python
sizes = [35, 25, 20, 20]
labels = ['Python', 'OpenCV', 'PyTorch', '其他']
explode = (0.05, 0.05, 0.05, 0.05)

ax.pie(sizes, labels=labels, autopct='%1.1f%%',
       explode=explode, startangle=90,
       shadow=False, wedgeprops={'edgecolor': 'white'})
```

### 4.8 热力图 / imshow

```python
matrix = np.random.rand(10, 10)
im = ax.imshow(matrix, cmap='viridis', aspect='auto')
plt.colorbar(im, ax=ax)

# 显示数值
for i in range(10):
    for j in range(10):
        ax.text(j, i, f'{matrix[i, j]:.2f}', ha='center', va='center', fontsize=8)
```

### 4.9 等值线 / 等高线

```python
x = np.linspace(-3, 3, 100)
y = np.linspace(-3, 3, 100)
X, Y = np.meshgrid(x, y)
Z = np.sin(X) * np.cos(Y)

ax.contour(X, Y, Z, levels=10, cmap='RdYlBu')
ax.contourf(X, Y, Z, levels=20, cmap='RdYlBu', alpha=0.6)  # 填充
```

### 4.10 3D 图

```python
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure(figsize=(10, 8))
ax = fig.add_subplot(111, projection='3d')

X, Y = np.meshgrid(np.linspace(-2, 2, 50), np.linspace(-2, 2, 50))
Z = np.sin(X) * np.cos(Y)

ax.plot_surface(X, Y, Z, cmap='viridis', alpha=0.8)
ax.set_title('3D 曲面图')
```

---

## 五、进阶技巧

### 5.1 双 y 轴

```python
fig, ax1 = plt.subplots()

x = np.arange(0, 10, 0.1)
ax1.plot(x, np.sin(x), 'b-', label='sin(x)')
ax1.set_ylabel('sin(x)', color='b')

ax2 = ax1.twinx()  # 共享x轴
ax2.plot(x, np.exp(x/10), 'r-', label='exp(x/10)')
ax2.set_ylabel('exp(x/10)', color='r')
```

### 5.2 样式风格

```python
# 查看所有可用样式
print(plt.style.available)

# 应用样式
plt.style.use('ggplot')
plt.style.use('seaborn-v0_8-darkgrid')
plt.style.use('fivethirtyeight')

# 临时使用
with plt.style.context('dark_background'):
    plt.plot(x, y, 'w-')
    plt.show()
```

### 5.3 动画

```python
from matplotlib.animation import FuncAnimation

fig, ax = plt.subplots()
x, y = [], []
line, = ax.plot([], [], 'r-')

def init():
    ax.set_xlim(0, 2*np.pi)
    ax.set_ylim(-1.5, 1.5)
    return line,

def update(frame):
    x.append(frame)
    y.append(np.sin(frame))
    line.set_data(x, y)
    return line,

ani = FuncAnimation(fig, update, frames=np.linspace(0, 2*np.pi, 100),
                    init_func=init, blit=True, interval=50)
```

### 5.4 保存图像

```python
# 基础保存
plt.savefig('figure.png', dpi=300, bbox_inches='tight')

# 格式选项
plt.savefig('figure.pdf')                            # 矢量PDF
plt.savefig('figure.svg')                            # 矢量SVG
plt.savefig('figure.png', dpi=300, bbox_inches='tight')  # 高清PNG

# 透明背景
plt.savefig('figure.png', transparent=True)
```

### 5.5 颜色映射

```python
# 查看所有色图
cmaps = [m for m in plt.colormaps() if not m.endswith('_r')]
print(sorted(cmaps))

# 常用色图分类
# 顺序: 'viridis', 'plasma', 'inferno', 'magma'  （新默认）
# 发散: 'RdBu', 'coolwarm', 'seismic'
# 循环: 'hsv', 'twilight'
# 定性: 'Set1', 'Set2', 'tab10', 'Pastel1'
```

### 5.6 secondary axis（辅助轴）

```python
fig, ax = plt.subplots()
ax.plot([0, 32, 100], [0, 90, 212], 'o-')  # 摄氏度

def c_to_f(c): return c * 9/5 + 32
def f_to_c(f): return (f - 32) * 5/9

secax = ax.secondary_yaxis('right', functions=(c_to_f, f_to_c))
secax.set_ylabel('华氏度 ℉')
ax.set_ylabel('摄氏度 ℃')
```

### 5.7 事件连接（交互）

```python
def on_click(event):
    if event.inaxes:
        print(f'点击坐标: ({event.xdata:.2f}, {event.ydata:.2f})')

fig, ax = plt.subplots()
ax.plot(x, y)
fig.canvas.mpl_connect('button_press_event', on_click)
```

---

## 六、新版知识点 (v3.6+)

### 6.1 subplot_mosaic — 不规则子图（v3.3+，强烈推荐）

```python
# 用类似ASCII画图的方式定义布局
fig, axes = plt.subplot_mosaic("""
    AAA
    BCD
""", figsize=(10, 6))

axes['A'].plot(x, y, title='主图')
axes['B'].hist(data, bins=30)
axes['C'].scatter(x, y)
axes['D'].bar(['a','b','c'], [1,2,3])
```

更复杂的布局：
```python
layout = """
    AABB
    AACC
    DDEE
"""
fig, axes = plt.subplot_mosaic(layout, figsize=(12, 8))
```

### 6.2 字符串格式化刻度（v3.1+）

```python
# 新版方式：直接在set_*formatter里用字符串模板
ax.xaxis.set_major_formatter('第{x:.0f}天')  # v3.1+
ax.xaxis.set_major_formatter('${x:.2f}')      # 美元格式
ax.xaxis.set_major_formatter('{x:.0f}%')      # 百分比

# 日期格式化
import matplotlib.dates as mdates
ax.xaxis.set_major_formatter(mdates.DateFormatter('%Y-%m'))
ax.xaxis.set_major_formatter('{x:%Y-%m-%d}')  # v3.1+ 简写
```

### 6.3 更灵活的 bar_label（v3.4+）

```python
bars = ax.bar(categories, values)

# 基本用法
ax.bar_label(bars, fmt='%.1f')

# 自定义标签
ax.bar_label(bars, labels=[f'{v}件' for v in values])

# 调整位置
ax.bar_label(bars, padding=5, fontsize=10, color='red')

# 柱内显示
ax.bar_label(bars, label_type='center', color='white', fontweight='bold')
```

### 6.4 colored / styled boxplot（v3.5+）

```python
# 新版 boxplot 更易定制颜色
bp = ax.boxplot(data, patch_artist=True)
for patch, color in zip(bp['boxes'], ['#4f46e5', '#22c55e', '#f59e0b']):
    patch.set_facecolor(color)
    patch.set_alpha(0.7)
```

### 6.5 改进的 3D 图

```python
# v3.5+ 3D 图性能和质量都有提升
ax.plot_surface(X, Y, Z, cmap='viridis', 
                rstride=1, cstride=1,     # 控制网格密度
                antialiased=True)          # 抗锯齿
```

### 6.6 更好的 str 类型转换 (v3.7+)

```python
# 直接在label等地方用更自由的字符串格式
ax.set_xlabel('数据 {:.1f}'.format(3.1415))  # 老方式
ax.set_xlabel(f'数据 {3.1415:.1f}')           # f-string（推荐）

# ticklabel 格式（v3.6+）
ax.ticklabel_format(style='scientific', scilimits=(-3, 4))
```

### 6.7 新 colormap 系统 (v3.5+)

```python
# 注册自定义色图
from matplotlib.colors import LinearSegmentedColormap
colors = ['#4f46e5', '#22c55e', '#f59e0b']
cmap = LinearSegmentedColormap.from_list('my_cmap', colors, N=256)
plt.register_cmap(cmap=cmap)

# 获取色图列表（v3.5+ API）
cmaps = plt.colormaps()  # 返回列表，更直观
```

### 6.8 布局引擎 (v3.6+)

```python
fig, axes = plt.subplots(2, 2, layout='constrained')
# 可选: 'constrained' (v3.6+, 推荐), 'compressed' (v3.6+), 'tight'
# constrained 比 tight_layout 更智能，支持colorbar等

# 或者手动设置
fig.set_layout_engine('constrained')
fig.set_layout_engine('tight')
```

### 6.9 改进的图例 (v3.5+)

```python
# 多列图例
ax.legend(ncols=2, fontsize=10)

# 图例外框
ax.legend(frameon=True, fancybox=True, shadow=True, 
          facecolor='white', edgecolor='black', framealpha=0.9)

# 简洁风格
ax.legend(fancybox=False, edgecolor='#ccc')
```

### 6.10 新版 mplcursors — 交互式鼠标悬停

```python
# 需安装: pip install mplcursors
import mplcursors

fig, ax = plt.subplots()
scatter = ax.scatter(x, y)
mplcursors.cursor(scatter).connect("add", lambda sel: sel.annotation.set_text(
    f'x={sel.target[0]:.2f}\ny={sel.target[1]:.2f}'
))
plt.show()
```

### 6.11 科技论文图表风格（很实用）

```python
# 模仿 Nature/Science 风格
plt.style.use('default')

fig, ax = plt.subplots(figsize=(4, 3))  # 窄长比例
ax.plot(x, y, color='black', linewidth=1)

# 去除右边和上边框线
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# 刻度朝内
ax.tick_params(direction='in', length=4)

# 更紧凑
fig.tight_layout(pad=0.5)
```

---

## 七、与我之前 OpenCV 练习的结合

你之前跑的 OpenCV 练手代码，其实已经用到了很多 Matplotlib 知识点：

| 你代码里的代码 | 对应知识点 |
|---|---|
| `plt.subplot(1, 3, 1)` | 子图布局(3.1) |
| `plt.imshow(img_rgb)` | imshow 显示图像(4.8) |
| `plt.hist(img.ravel(), bins=256)` | 直方图(4.5) |
| `plt.title('亮度 -60')` | 标题/中文(2.5) |
| `plt.axis('off')` | 坐标轴控制(2.3) |
| `plt.tight_layout()` | 布局调整(6.8) |
| `plt.figure(figsize=(15, 5))` | 画布创建(1.2) |
| `np.hstack([img1, img2])` 配合 `plt.imshow` | 图像拼接显示 |

结合了你正在做的方向，重点掌握：
- **imshow** + colormap → 显示图像处理结果
- **subplots** → 并排对比原图和效果图
- **hist** → 分析像素分布
- **savefig** → 保存实验结果写论文用

---

## 八、速查表

### 创建与保存

| 操作 | 代码 |
|------|------|
| 创建画布 | `fig, ax = plt.subplots()` |
| 指定大小 | `plt.subplots(figsize=(8, 4))` |
| 多子图 | `plt.subplots(2, 3)` |
| 保存 | `plt.savefig('fig.png', dpi=300)` |
| 显示 | `plt.show()` |

### 常用属性设置

| 设置 | 代码 |
|------|------|
| 标题 | `ax.set_title('text')` |
| x标签 | `ax.set_xlabel('text')` |
| y标签 | `ax.set_ylabel('text')` |
| x范围 | `ax.set_xlim(0, 10)` |
| y范围 | `ax.set_ylim(0, 10)` |
| 图例 | `ax.legend()` |
| 网格 | `ax.grid(True)` |
| 紧凑布局 | `fig.tight_layout()` 或 `layout='constrained'` |

### 绘图命令

| 图表 | 代码 |
|------|------|
| 折线图 | `ax.plot(x, y)` |
| 散点图 | `ax.scatter(x, y)` |
| 柱状图 | `ax.bar(x, height)` |
| 直方图 | `ax.hist(data, bins=30)` |
| 饼图 | `ax.pie(sizes)` |
| 箱线图 | `ax.boxplot(data)` |
| 热力图 | `ax.imshow(matrix)` |
| 等值线 | `ax.contour(X, Y, Z)` |
| 填充 | `ax.fill_between(x, y1, y2)` |

### 中文字体

```python
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']
plt.rcParams['axes.unicode_minus'] = False
```

---

> **💡 莫烦系列学习路线建议：**
> 学完本阶段的 Python + Matplotlib 后，你的下一步是 **阶段二 · 机器学习**。
> 到时候 Matplotlib 会反复用来画学习曲线、分类结果可视化，会越用越熟。
