# Titanic Survival Prediction | 泰坦尼克号生还预测

> 从 C 语言到机器学习的完整学习记录 —— 一个零基础入门 AI 的自我训练项目

---

##  项目简介

使用泰坦尼克号乘客数据，训练随机森林模型预测乘客是否生还。

最新准确率：**0.77272**（Kaggle 排行榜）

---

## 学习路径

这个项目是我整个技术成长路线中的**第一步**，记录了从底层语言到 AI 的跨越：

```
C 语言基础（指针、结构体、数组）→ 数据结构与算法 → 机器学习入门（本项目）
```

之前在学 C 语言时，我习惯了自己管理内存、手动匹配括号、定义结构体来组织数据。转到 Python + pandas 后，我发现：

| 在 C 里我必须... | 在 Python/pandas 里... |
|---|---|
| 手动 `#include` 每个库 | 一行 `import pandas as pd` |
| 手写字符串匹配函数找列 | `train_data[features]` 直接按名取列 |
| 手动分配和释放内存 | pandas 自动管理内存 |
| 用 `if-else` 写决策逻辑 | 随机森林自动从数据中学习 `if-else` 规则 |

> **这个项目的价值不只是跑通了 0.77 的准确率，更是让我用"写 C 程序"的底层思维，理解了"调库写 AI"的上层抽象。** 每一行代码背后发生了什么，我都能从 C 语言的角度推演出来。

---

## 技术栈

- **Python 3** / **pandas** / **scikit-learn**
- **Jupyter Notebook**（实验型编程，边跑边看结果）

---

## 核心流程

### Step 1：数据加载
读取 Kaggle 提供的训练集和测试集。

```python
train_data = pd.read_csv("/kaggle/input/competitions/titanic/train.csv")
test_data = pd.read_csv("/kaggle/input/competitions/titanic/test.csv")
```

### Step 2：特征工程（挑选输入）
只选取与生还率直觉相关的特征。

```python
features = ["Pclass", "Sex", "SibSp", "Parch"]
```

### Step 3：One-Hot 编码（文字转数字）
模型只认 `0/1`，所以把文字分类 `"male"/"female"` 转成独立的 0/1 列。

```python
X = pd.get_dummies(train_data[features])
```

### Step 4：定义目标变量（要预测什么）
`Survived` 列就是数据集给出的标准答案。

```python
y = train_data["Survived"]  # 0=未生还, 1=生还
```

### Step 5：训练模型（让模型自己学规则）
随机森林 = 100 棵决策树投票。类比 C 语言：就是调一个 `fit(train, answer)` 函数。

```python
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier(n_estimators=100)
model.fit(X, y)
```

### Step 6：预测 + 生成提交文件
```python
predictions = model.predict(X_test)
submission = pd.DataFrame({"PassengerId": test_data.PassengerId, "Survived": predictions})
submission.to_csv("submission.csv", index=False)
```

---

## 学习收获

- ✅ **理解了监督学习的基本范式**：特征 X → 标签 y → 模型训练 → 预测
- ✅ **掌握了 pandas 数据预处理流程**：特征选择、One-Hot 编码、数据对齐
- ✅ **理解了 One-Hot Encoding 的作用与必要性**：为什么模型看不懂文字
- ✅ **跑通了 Kaggle 竞赛的完整提交流程**：从代码到分数
- ✅ **建立了"从 C 到 Python"的思维迁移**：理解了高级库的底层封装逻辑

---

## 后续优化方向

- [ ] 加入 `Age`、`Fare` 特征，并使用 `fillna()` 处理缺失值
- [ ] 尝试特征交叉（如 `FamilySize = SibSp + Parch`）
- [ ] 对比逻辑回归 / XGBoost 的效果差异
- [ ] 学习用 `scikit-learn Pipeline` 规范化特征工程流程

---

## 文件说明

| 文件 | 说明 |
|---|---|
| `Titanic_prediction.ipynb` | 完整的 Jupyter Notebook，包含全部代码与运行过程 |
| `README.md` | 本说明文件 |

---

*This project marks the starting point of my journey from embedded/C programming to machine learning and AI. It documents how I learned to think in terms of features, labels, and models — and how my C-background gave me a solid foundation for understanding what happens under
