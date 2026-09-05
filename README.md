# Titanic Survival Prediction | 泰坦尼克号生还预测

> Kaggle 上的入门项目，主要是我自己开始学习完整跑通机器学习从数据到提交的流程

## 项目简介
使用泰坦尼克号乘客数据，训练随机森林模型预测乘客是否生还。
- **最新准确率**：0.77272（Kaggle 排行榜）
- **模型**：Random Forest (n_estimators=100)
- **特征**：Pclass, Sex, SibSp, Parch（经 One-Hot 编码处理）

## 技术栈
- Python / pandas / scikit-learn
- Jupyter Notebook

## 核心流程
1. **数据加载**：读取 train.csv / test.csv
2. **特征工程**：选取关键特征，使用 `pd.get_dummies()` 将分类变量转为数值
3. **模型训练**：RandomForestClassifier 拟合训练集
4. **预测与提交**：生成 submission.csv 提交至 Kaggle

## 学习收获
- 理解了监督学习的基本范式（特征 X → 标签 y → 模型训练 → 预测）
- 掌握了 pandas 数据预处理流程
- 理解了 One-Hot Encoding 的作用与必要性
- 跑通了 Kaggle 竞赛的完整提交流程

## 后续优化方向
- [ ] 加入 Age、Fare 特征并处理缺失值
- [ ] 尝试特征交叉（如 FamilySize = SibSp + Parch）
- [ ] 对比逻辑回归 / XGBoost 的效果
