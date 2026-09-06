# Titanic Survival Prediction | 泰坦尼克号生还预测

> 从 C 语言到机器学习的完整学习记录 —— 一个零基础入门 AI 的自我训练项目

## 项目简介

使用泰坦尼克号乘客数据，训练随机森林模型预测乘客是否生还。

- **最新准确率**：0.75837（V8，当前版本）
- **最佳准确率**：0.77272（V2，基线版本）
- **模型**：RandomForestClassifier (n_estimators=100, random_state=91)
- **特征数**：9 个（含 2 个衍生特征 + 2 个分箱特征）

## 学习路径

这个项目是我整个技术成长路线中的第一步，记录了从底层语言到 AI 的跨越：

**C 语言基础（指针、结构体、数组）→ 数据结构与算法 → 机器学习入门（本项目）**

之前在学 C 语言时，我习惯了自己管理内存、手动匹配括号、定义结构体来组织数据。转到 Python + pandas 后，我发现：

| 在 C 里我必须... | 在 Python/pandas 里... |
|---|---|
| 手动 `#include` 每个库 | 一行 `import pandas as pd` |
| 手写循环给数组赋值 | `fillna(median())` 向量化一行搞定 |
| 手写字符串匹配函数找列 | `train_data['Name'].str.extract(r'...')` |
| 手动分配和释放内存 | pandas 自动管理内存 |
| 用 if-else 写决策逻辑 | 随机森林自动从数据中学习 if-else 规则 |
| 手动对齐结构体字段 | `reindex(columns=x.columns, fill_value=0)` |

这个项目的价值不只是跑通了 0.77 的准确率，更是让我用"写 C 程序"的底层思维，理解了"调库写 AI"的上层抽象。**每一行代码背后发生了什么，我都能从 C 语言的角度推演出来。**

## 技术栈

- Python 3 / pandas / scikit-learn
- Jupyter Notebook（实验型编程，边跑边看结果）

## 核心流程

| Step | 操作 | 关键代码 |
|---|---|---|
| 1. 数据加载 | 读取 Kaggle 训练集/测试集 | `pd.read_csv(...)` |
| 2. 缺失值处理 | 用**训练集中位数**填 NaN（防数据泄露） | `fillna(train_data[col].median())` |
| 3. 年龄分箱 | `pd.cut` 按数值边界等宽切 4 段 | `pd.cut(Age, bins=[0,12,18,60,100])` |
| 4. 票价分箱 | `pd.qcut` 按人数比例等频切 3 档 | `pd.qcut(Fare, 3)` |
| 5. 衍生特征 | 家庭大小 + 是否独自出行 | `FamilySize = SibSp + Parch + 1` |
| 6. 头衔提取 | 正则从姓名抠出 Title | `str.extract(r' ([A-Z][a-z]+)\.')` |
| 7. One-Hot 编码 | 文字分类转 0/1 独立列 | `pd.get_dummies()` |
| 8. 特征对齐 | 保证训练/预测维度一致 | `reindex(columns=x.columns, fill_value=0)` |
| 9. 训练模型 | 100 棵决策树投票 | `RandomForestClassifier(random_state=91)` |
| 10. 预测提交 | 生成 submission.csv | `model.predict(x_test)` |

## 使用的 9 个特征

| 特征 | 来源 | 作用 |
|---|---|---|
| Pclass | 原始 | 头等舱生还率远高于三等舱 |
| Sex | 原始 | 女性生还率远高于男性 |
| SibSp / Parch | 原始 | 家庭结构影响生还率 |
| Age_Bin | 分箱衍生 | 儿童/老年优先级不同 |
| Fare_Bin | 分箱衍生 | 同舱位内高票价位置更靠前 |
| FamilySize | 数值衍生 | 小家庭（2~4人）生还率最高 |
| IsAlone | 0/1 衍生 | 独自出行生还率偏低 |
| Title | 字符串提取 | 头衔浓缩阶层+性别+年龄 |

## 关键技术要点

### 1. 数据泄露（Data Leakage）
测试集的缺失值填充**必须用训练集的统计量**，不能直接用测试集自身的 median。否则模型在训练时已经"偷看"了测试集的信息，导致线上分数虚高、泛化能力差。

### 2. 分箱的选择
- **`pd.cut`（等宽）**：按数值范围均分，适合分布均匀的数据
- **`pd.qcut`（等频）**：按人数均分，适合有极端离群值偏斜的数据（如票价）

### 3. 随机种子
`random_state=91` 固定了随机森林的随机采样过程，保证每次运行的分数可复现。不加这个参数时，决策树的随机分裂每次不同，准确率会在 0.67~0.77 之间浮动。

### 4. 特征对齐
`reindex(columns=x.columns, fill_value=0)` 以训练集的特征列为标准，测试集缺的列补 0、多的列删掉。如果独热编码后两边维度不一致，直接 `predict()` 会报 `KeyError`。

## 学习收获

✅ 理解了监督学习的基本范式：特征 X → 标签 y → 模型训练 → 预测

✅ 掌握了 pandas 数据预处理的完整流程：缺失值处理、特征分箱、衍生特征、字符串正则提取、One-Hot 编码、特征对齐

✅ 理解了**数据泄露**的本质和避免方法

✅ 理解了**随机种子**对实验可复现性的意义

✅ 建立了"从 C 到 Python"的思维迁移：底层数组思维 → 高层数据科学生态的理解

✅ 跑通了 Kaggle 竞赛的完整提交流程

## 后续优化方向

- [ ] **分组中位数填充**：按 Pclass + Sex 分组填充 Age，比全局中位数更精准
- [ ] **特征交叉**：尝试 Age × Pclass 等组合特征
- [ ] **模型对比**：对比 LogisticRegression / GradientBoosting / XGBoost 的效果
- [ ] **使用 sklearn Pipeline**：把数据预处理和模型训练串成一条管线，规范化流程
- [ ] **特征重要性分析**：用 `model.feature_importances_` 看看模型到底最看重哪些特征

---

*This project marks the starting point of my journey from embedded/C programming to machine learning and AI. It documents how I learned to think in terms of features, labels, and models — and how my C-background gave me a solid foundation for understanding what happens underneath the high-level abstractions.*

## 文件说明

| 文件 | 说明 |
|---|---|
| Titanic_prediction.ipynb | 完整 Jupyter Notebook，包含全部代码、注释与运行过程 |
| README.md | 本说明文件 |

## 技术栈

- Python 3 / pandas / scikit-learn
- Jupyter Notebook
