# Titanic Survival Prediction | 泰坦尼克号生还预测

使用 Python、pandas 和 scikit-learn，通过随机森林预测乘客是否生还。这是从 C 语言学习转向机器学习的入门项目。

## 当前版本

推荐入口为 `Titanic_prediction.ipynb`，已将最新上传的 `notebook9d78c9203f.ipynb` 整理为分步骤、有说明的主 Notebook，保留原实验计算逻辑。

- 模型：`RandomForestClassifier(n_estimators=100, random_state=91)`。
- 当前输入特征共 5 个，独热编码后列数会增加。
- 历史 README 记录 Kaggle Accuracy 为 **0.77751（V13）**。仓库没有提交记录证明其对应哪份 Notebook，因此不将其标为整理后版本的新成绩。
- 尚无本地验证或交叉验证结果，不能仅凭特征数量判断版本优劣。

## 运行方法

1. 在 Kaggle 创建 Notebook，添加 Titanic 竞赛数据。
2. 导入 `Titanic_prediction.ipynb`，确认数据位于 `/kaggle/input/competitions/titanic/`。
3. 从头运行全部单元格，无需 GPU。
4. 将生成的 `submission.csv` 提交到 Titanic 竞赛并记录对应版本和成绩。

提交文件包含 `PassengerId` 和 `Survived` 两列。Accuracy 是预测正确的比例；测试集没有公开标签，不能在本地直接算出 Kaggle 成绩。

## 当前特征

| 特征 | 处理方式 | 设计目的 |
|---|---|---|
| Pclass | 原始舱位等级 | 检验舱位与生还的关联 |
| Sex | 独热编码 | 检验性别与生还的关联 |
| Age_Bin | 按 [0, 12, 18, 60, 100] 分箱并独热编码 | 表示年龄阶段 |
| FamilySize | SibSp + Parch + 1 | 表示含本人在内的同行家庭人数 |
| IsAlone | FamilySize == 1 | 标记是否独自出行 |

这些是特征设计假设，是否提升效果仍需实验验证。编码后使用 `reindex(columns=x.columns, fill_value=0)` 对齐训练和测试的列。

## 文件与版本

| 文件 | 内容 |
|---|---|
| Titanic_prediction.ipynb | 推荐入口，整理后的最新 5 特征实验 |
| notebook9d78c9203f.ipynb | 最新 Kaggle 导出原件，保持不变以便核对 |
| Git 历史中的主 Notebook | 早期 9 特征实验，包含 Fare_Bin、Title 等 |

本次整理保留最新实验的填充方式、年龄边界、模型参数和预测逻辑。移除了无关的 Kaggle 模板代码，拆分步骤，更新说明并清空主 Notebook 的运行状态，未加入旧版特征。

## 复盘与待改进

1. **预处理一致性**：当前训练集和测试集分别使用自身中位数填充 Age、Fare。后续应从训练部分计算统计量，再应用到验证和测试部分。这不等于读取生还标签，也不能直接断言线上分数一定虚高。Fare 当前未进入模型，其填充不影响预测。
2. **分箱含义**：指定年龄边界是不等宽分箱，默认左开右闭，0 或超出边界的值会成为缺失值。旧版对训练和测试分别做票价 qcut 会产生不同边界；最新版本已不使用票价分箱。
3. **实验可比较性**：固定 random_state 有助于相同环境下复现结果，不能据此将历史分数波动全部归因于种子。应固定验证划分，每次改变一个因素并记录结果。

下一步先划分训练和验证集，仅用训练部分拟合预处理规则；记录基线和两次特征改动的验证 Accuracy、Kaggle 分数与结论。无提升也如实记录。
