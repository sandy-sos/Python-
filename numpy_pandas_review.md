# NumPy & Pandas 知识点速查表

> 📅 整理时间：2026-07-05 | 配套学习：Matplotlib

---

# 第一部分：NumPy

## 一、核心对象：ndarray

```python
import numpy as np

# ========== 创建数组 ==========
np.array([1, 2, 3])                 # 从列表创建
np.zeros((3, 4))                    # 全零矩阵
np.ones((2, 3))                     # 全一矩阵
np.eye(4)                           # 单位矩阵
np.full((2, 3), 7)                  # 全是 7
np.arange(0, 10, 2)                 # [0, 2, 4, 6, 8]  类似 range
np.linspace(0, 1, 5)                # [0, 0.25, 0.5, 0.75, 1]  等间距

# ========== 查看属性 ==========
arr = np.random.randn(3, 4)
arr.shape           # (3, 4)
arr.ndim            # 维度数 → 2
arr.size            # 元素总数 → 12
arr.dtype           # 数据类型 → float64
arr.T               # 转置 → (4, 3)
```

## 二、索引与切片

```python
arr = np.arange(12).reshape(3, 4)
# array([[ 0,  1,  2,  3],
#        [ 4,  5,  6,  7],
#        [ 8,  9, 10, 11]])

# ========== 基本索引（和列表类似）==========
arr[0]              # 第一行 → [0, 1, 2, 3]
arr[0, 1]           # 第 0 行第 1 列 → 1
arr[:2]             # 前两行
arr[:, 1:3]         # 所有行的第 1~2 列
arr[::-1]           # 行倒序
arr[1:, ::2]        # 从第 1 行起，每隔一列取一列

# ========== 花式索引（Fancy Indexing）==========
arr[[0, 2]]         # 选取第 0 行和第 2 行
arr[[0, 2], [1, 3]] # arr[0,1] 和 arr[2,3]
arr[[0, 2]][:, [1,3]]  # 先选行再选列

# ========== 布尔索引 ==========
arr > 5             # 返回布尔矩阵
arr[arr > 5]        # 取出所有 > 5 的元素 → 一维数组
arr[(arr > 3) & (arr < 9)]  # 与条件
arr[(arr < 3) | (arr > 9)]  # 或条件
```

## 三、形状操作

```python
arr = np.arange(12)

arr.reshape(3, 4)       # 重塑为 3×4（返回新数组）
arr.resize(3, 4)        # 原地重塑（修改原数组）
arr.flatten()           # 展平为一维（返回副本）
arr.ravel()             # 展平（尽可能返回视图，省内存）
arr.T                   # 转置

np.newaxis              # 增加维度
arr[np.newaxis, :]      # 变成 (1, 12)
arr[:, np.newaxis]      # 变成 (12, 1)

np.expand_dims(arr, axis=0)  # 同上，显式写法
np.squeeze(arr)              # 去掉所有长度为 1 的维度
```

## 四、拼接与分割

```python
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

# ========== 拼接 ==========
np.vstack((a, b))       # 垂直拼接 → (4, 2)
np.hstack((a, b))       # 水平拼接 → (2, 4)
np.concatenate([a, b], axis=0)  # 等价 vstack
np.concatenate([a, b], axis=1)  # 等价 hstack

# ========== 分割 ==========
np.split(arr, 3)        # 均分为 3 份
np.vsplit(arr, 3)       # 垂直切分
np.hsplit(arr, 3)       # 水平切分
```

## 五、计算：通用函数（ufunc）

```python
# ========== 算术运算（均为逐元素）==========
arr + 2             # 广播加法
arr1 + arr2         # 对应元素相加
arr - 5, arr * 2, arr / 3, arr ** 2
np.sqrt(arr), np.log(arr), np.exp(arr)
np.abs(arr), np.sin(arr), np.cos(arr)

# ========== 聚合操作 ==========
arr.sum()           # 所有元素和
arr.mean()          # 平均值
arr.std()           # 标准差
arr.var()           # 方差
arr.min() / arr.max()
arr.argmin() / arr.argmax()  # 最值索引
arr.cumsum()        # 累积和
arr.cumprod()       # 累积积

# ========== 沿轴操作 ==========
arr.sum(axis=0)     # 每列求和 → (4,)  (压缩行)
arr.sum(axis=1)     # 每行求和 → (3,)  (压缩列)
arr.mean(axis=1)    # 按行求平均
```

> **axis 记忆诀窍**：`axis=0` 是跨行操作（纵向），`axis=1` 是跨列操作（横向）

## 六、广播机制（Broadcasting）⭐ 核心

```python
# NumPy 自动扩展小数组以匹配大数组的形状
arr = np.ones((3, 4))
arr + np.array([1, 2, 3, 4])    # (3,4) + (4,) → 每行加 [1,2,3,4]
arr + np.array([[1], [2], [3]]) # (3,4) + (3,1) → 每列加 [1,2,3]

# 广播规则：
# 1. 从尾部维度开始对齐
# 2. 维度匹配或其中一个为 1 即可广播
# 3. 维度为 1 的会被"拉伸"到匹配
```

## 七、随机数

```python
rng = np.random.default_rng(seed=42)   # ✅ 推荐新写法
rng.random((3, 3))                     # [0,1) 均匀分布
rng.normal(0, 1, (3, 3))              # 标准正态分布
rng.randint(0, 10, 5)                 # 随机整数
rng.choice([1, 2, 3, 4, 5], size=3)   # 随机抽选
rng.shuffle(arr)                       # 洗牌（原地打乱）

# ── 旧的写法（莫烦教的，仍然能用）──
np.random.rand(3, 3)          # [0,1) 均匀
np.random.randn(3, 3)         # 标准正态
np.random.randint(0, 10, 5)
np.random.seed(42)            # 设置种子
```

## 八、线性代数

```python
np.dot(a, b)            # 矩阵乘法
a @ b                   # 等价写法（推荐，Python 3.5+）
np.linalg.inv(a)        # 逆矩阵
np.linalg.det(a)        # 行列式
np.linalg.eig(a)        # 特征值 / 特征向量
np.linalg.svd(a)        # SVD 分解
np.linalg.norm(a)       # 范数
```

---

# 第二部分：Pandas

## 一、两种核心数据结构

```python
import pandas as pd

# ========== Series（一维，带标签）==========
s = pd.Series([1, 3, 5, np.nan, 7])
s = pd.Series([1, 3, 5], index=['a', 'b', 'c'])

s.values        # ndarray
s.index         # 索引对象
s.name          # 系列名称

# ========== DataFrame（二维，表格）==========
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Charlie'],
    'age':  [25, 30, 35],
    'city': ['Shanghai', 'Beijing', 'Hangzhou']
})

df = pd.DataFrame(np.random.randn(3, 4),
                  columns=['A', 'B', 'C', 'D'],
                  index=['row1', 'row2', 'row3'])
```

## 二、数据读写

```python
pd.read_csv('file.csv')             # CSV
pd.read_csv('file.csv', encoding='utf-8')
pd.read_excel('file.xlsx')          # Excel
pd.read_excel('file.xlsx', sheet_name='Sheet1')
pd.read_json('file.json')           # JSON
pd.read_html('url')                 # 网页表格

# 写入
df.to_csv('out.csv', index=False)
df.to_excel('out.xlsx', index=False)
df.to_json('out.json')
```

## 三、快速浏览数据

```python
df.head(10)         # 前 10 行
df.tail(5)          # 后 5 行
df.sample(3)        # 随机抽 3 行
df.info()           # 概览：行数、列数、类型、非空数
df.describe()       # 数值列的统计摘要（均值、标准差、分位数等）
df.dtypes           # 每列数据类型
df.columns          # 列名列表
df.index            # 行索引
df.shape            # 形状 (行, 列)
df.values           # 转为 ndarray
```

## 四、选取数据 ⭐ 最常用

```python
# ========== 列操作 ==========
df['name']              # 选一列 → Series
df[['name', 'age']]     # 选多列 → DataFrame
df.name                 # 列名合法时可用（不推荐）

# ========== 行操作（按标签）==========
df.loc[0]               # 索引为 0 的行
df.loc[0:3]             # 索引 0 到 3（**包含右端点**）
df.loc[[0, 2, 4]]       # 指定行

# ========== 行操作（按位置）==========
df.iloc[0]              # 第 0 行
df.iloc[0:3]            # 第 0~2 行（**不包含右端点**，和 Python 切片一致）
df.iloc[[0, 2, 4]]
df.iloc[:, 0:2]         # 所有行，前两列

# ========== 行列混合 ==========
df.loc[0:3, ['name', 'age']]
df.iloc[1:4, 0:2]
df.at[0, 'name']        # 快速取单个值（by 标签）
df.iat[0, 1]            # 快速取单个值（by 位置）

# ========== 条件筛选 ==========
df[df['age'] > 30]
df[(df['age'] > 25) & (df['city'] == 'Shanghai')]
df[df['name'].isin(['Alice', 'Bob'])]
df[df['age'].between(25, 35)]
df[df['name'].str.contains('li', case=False)]
```

## 五、处理缺失值

```python
df.isna()               # 是否缺失 → 布尔 DataFrame
df.isna().sum()         # 每列缺失个数
df.isnull().sum()       # 同上，isna 别名

# 删除缺失值
df.dropna()             # 删除含缺失值的行
df.dropna(axis=1)       # 删除含缺失值的列
df.dropna(thresh=2)     # 至少有 2 个非空值才保留

# 填充缺失值
df.fillna(0)                     # 用 0 填充
df.fillna(df.mean())             # 用该列均值填充
df.fillna(method='ffill')        # 前向填充
df.fillna(method='bfill')        # 后向填充
df.interpolate()                 # 线性插值
```

## 六、数据清洗与变换

```python
# ========== 排序 ==========
df.sort_values('age', ascending=False)
df.sort_index()

# ========== 修改列名 ==========
df.rename(columns={'old': 'new'})
df.columns = ['A', 'B', 'C']        # 直接替换

# ========== 添加 / 删除列 ==========
df['new_col'] = df['a'] + df['b']
df['age_sq'] = df['age'] ** 2
df.drop('col_name', axis=1, inplace=True)
df.drop([0, 1], axis=0)             # 删除行

# ========== 去重 ==========
df.drop_duplicates()
df.drop_duplicates(subset=['name'])  # 按指定列去重
df.drop_duplicates(keep='last')      # 保留最后出现的

# ========== 类型转换 ==========
df['col'].astype(float)
pd.to_numeric(df['col'], errors='coerce')  # 强制转数值，失败变 NaN
pd.to_datetime(df['date'])                  # 转日期
```

## 七、分组与聚合 ⭐

```python
# ========== groupby 基础 ==========
df.groupby('city')['age'].mean()            # 按城市分组，求年龄均值
df.groupby('city').agg({'age': 'mean', 'name': 'count'})

# ========== 常用聚合函数 ==========
df.groupby('city').mean()
df.groupby('city').sum()
df.groupby('city').max() / .min()
df.groupby('city').std() / .var()
df.groupby('city').count()
df.groupby('city').nunique()   # 唯一值个数
df.groupby('city')['age'].describe()  # 详细的统计摘要

# ========== 多级分组 ==========
df.groupby(['city', 'gender'])['age'].mean()

# ========== agg 灵活用法 ==========
df.groupby('city')['age'].agg(['mean', 'std', 'min', 'max'])
df.groupby('city').agg(
    avg_age=('age', 'mean'),
    max_salary=('salary', 'max')
)
```

> **常见误区**：`groupby` 是惰性的，不加聚合函数不会执行。`df.groupby('city')` 只是一个 GroupBy 对象。

## 八、合并与连接

```python
# ========== concat（纵向/横向堆叠）==========
pd.concat([df1, df2])               # 纵向堆叠
pd.concat([df1, df2], axis=1)       # 横向拼接
pd.concat([df1, df2], ignore_index=True)  # 重置索引

# ========== merge（类似 SQL JOIN）==========
pd.merge(df1, df2, on='key')                # 内连接
pd.merge(df1, df2, on='key', how='inner')   # 内连接（默认）
pd.merge(df1, df2, on='key', how='outer')   # 外连接
pd.merge(df1, df2, on='key', how='left')    # 左连接
pd.merge(df1, df2, on='key', how='right')   # 右连接
pd.merge(df1, df2, left_on='key1', right_on='key2')  # 不同列名

# ========== join（基于索引的连接）==========
df1.join(df2)                       # 按索引连接
df1.join(df2, how='outer')
```

## 九、apply 家族（逐行/逐列操作）

```python
# ========== apply（DataFrame 的行或列）==========
df['age'].apply(lambda x: x + 1)              # Series → 每个元素
df[['a', 'b']].apply(np.sum, axis=0)          # 每列求和
df[['a', 'b']].apply(np.sum, axis=1)          # 每行求和

# ========== map（Series，一对一映射）==========
df['city'].map({'Shanghai': 'SH', 'Beijing': 'BJ'})

# ========== applymap（DataFrame 所有元素）==========
df.applymap(lambda x: f'{x:.2f}')             # 格式化所有数值
# ⚠️ Pandas 2.1+ 改用 .map() 代替 applymap
```

## 十、数据透视表

```python
pd.pivot_table(df, values='age',
               index='city', columns='gender',
               aggfunc='mean', fill_value=0)

# 等价于 Excel 的数据透视表
# index = 行标签, columns = 列标签, values = 要聚合的列
```

## 十一、时间序列基础（学完 Matplotlib 经常用到）

```python
# 生成日期序列
dates = pd.date_range('2026-01-01', periods=10, freq='D')
dates = pd.date_range('2026-01-01', '2026-12-31', freq='M')

# 设置时间索引
df.index = pd.to_datetime(df['date'])

# 重采样
df.resample('M').mean()     # 按月聚合
df.resample('W').sum()      # 按周聚合
df.resample('Q').max()      # 按季度聚合

# 滑动窗口
df['rolling_mean'] = df['value'].rolling(window=7).mean()
df['rolling_std']  = df['value'].rolling(window=7).std()
```

## 十二、Pandas 绘图（衔接 Matplotlib）

```python
import matplotlib.pyplot as plt

df['age'].plot()                        # 折线图
df.plot(kind='line')                    # 折线图
df.plot(kind='bar')                     # 柱状图
df.plot(kind='hist', bins=20)           # 直方图
df.plot(kind='scatter', x='a', y='b')  # 散点图
df.plot(kind='box')                     # 箱线图
df.plot(kind='kde')                     # 密度图

# 设置中文字体（和 Matplotlib 配合）
plt.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
plt.rcParams['axes.unicode_minus'] = False

plt.show()
```

---

# 第三部分：NumPy → Pandas → Matplotlib 衔接脉络

理清三个库的**数据流向**，对复习和后面理解图像处理很重要：

```
原始数据（CSV / Excel / 图片）
    │
    ▼
Pandas（读入、清洗、筛选、分组）
    │ .values  /  df.to_numpy()
    ▼
NumPy（矩阵运算、广播、数学变换）
    │
    ▼
Matplotlib（可视化：折线、直方图、热力图）
    │
    ▼
最终输出（论文图表 / 分析报告）
```

**核心衔接 API：**
```python
# Pandas → NumPy
arr = df.values          # 老写法
arr = df.to_numpy()      # 推荐新写法

# NumPy → Pandas
df = pd.DataFrame(arr, columns=[...])

# NumPy → Matplotlib
plt.plot(arr[:, 0], arr[:, 1])

# Pandas → Matplotlib
df.plot(x='col1', y='col2')  # 本质是封装了 plt.plot
```

---

# 第四部分：易错点 ⚠️

## NumPy 易错

1. **视图 vs 副本**：切片返回的是**视图**（修改会影响原数组），花式索引和布尔索引返回**副本**
   ```python
   a = np.arange(12).reshape(3, 4)
   b = a[0]          # 视图
   b[0] = 999        # a[0,0] 也变成 999！
   c = a[[0, 1]]     # 副本
   c[0, 0] = 0       # a 不受影响
   ```

2. **axis 方向混淆**：`axis=0` 向下操作（跨行），`axis=1` 向右操作（跨列）

3. **`=` 不复制**：`b = a` 只是别名，`b = a.copy()` 才是真复制

4. **整数索引 vs 切片维度不同**：
   ```python
   arr[:, 1]      # 一维数组 (3,)
   arr[:, 1:2]    # 二维数组 (3, 1)
   ```

## Pandas 易错

1. **loc 包含右端点，iloc 不包含**
   ```python
   df.loc[0:3]     # 取索引 0,1,2,3（4 行）
   df.iloc[0:3]    # 取第 0,1,2 行（3 行）
   ```

2. **链式赋值警告**：`df[df['a']>0]['b'] = 5` 会警告，改为 `df.loc[df['a']>0, 'b'] = 5`

3. **inplace=True 争议**：`df.dropna(inplace=True)` 很多人用，但官方更推荐 `df = df.dropna()`

4. **groupby 是惰性的**：忘了加聚合函数，只会返回一个 GroupBy 对象

---

# 快速自测清单 ✅

用它来检查自己是否掌握了：

## NumPy
- [ ] 创建：`zeros`, `ones`, `arange`, `linspace`, `eye`
- [ ] 属性：`shape`, `dtype`, `ndim`, `size`
- [ ] 索引：基本切片、花式索引、布尔索引
- [ ] 形状操作：`reshape`, `flatten`, `newaxis`
- [ ] 拼接：`vstack`, `hstack`, `concatenate`
- [ ] 聚合：`sum`, `mean`, `std`, `argmin`
- [ ] 广播：理解 (3,4) + (4,) 和 (3,4) + (3,1)
- [ ] 随机：新写法 `default_rng`
- [ ] 视图 vs 副本：知道什么时候修改会影响原数组

## Pandas
- [ ] 创建：`DataFrame(dict)`, `read_csv`, `read_excel`
- [ ] 浏览：`head`, `info`, `describe`, `dtypes`
- [ ] 选取：`loc`, `iloc`, 条件筛选
- [ ] 缺失值：`dropna`, `fillna`
- [ ] 分组：`groupby` + `agg`
- [ ] 合并：`merge` 的四种 join
- [ ] 排序 / 去重 / 修改列名
- [ ] Pandas → NumPy 转换：`to_numpy()`

---

> 💡 **复习建议**：打开 Jupyter Notebook，对照这份速查表把每个例子敲一遍，比光看印象深 10 倍。
> 敲完之后就可以开始 Matplotlib 了，它和 Pandas 的 `.plot()` 一脉相承，学起来会很顺。
