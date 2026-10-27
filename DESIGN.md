# Red-Team-Driven Safety Repair for Agentic Models — Track 2 设计书

> **赛事**：Lexsi AI Safety & Alignment Hackathon（Lexsi Labs × Cohere Labs ×
> Hugging Face），2026-11-01 ~ 11-21，全程线上。
> **赛道**：Track 2 — AI Red Teaming, Safety and Alignment。
> **奖金性质（如实记录）**：头奖 $3,662 **为 Lexsi/Cohere 额度 + 6 个月 HF Pro**，
> 非现金；另有 $20 HF 额度（注册+进 Discord，前 800 队）、入围再加 $200。
> 附带价值：Demo Day 面试机会（DevRel/Research Intern）。
> **资格（官方逐字）**："open to builders worldwide"，1–3 人队，solo 可报，
> 免费，注册 11-05 截止，交稿 11-14 EOD，Demo Day 11-22。

---

## 1. 一句话论点

把**智能体时代的红队语料**（不是聊天越狱）灌进 CuratorKIT → SafeTune →
CircuitKIT → AuditKIT 四件套，修一个小型开放权重模型（Cohere Tiny Aya），
并同时报告**攻击成功率下降**与**误拒率不升**——后者是绝大多数参赛作品
不会报的成本侧指标。

## 2. 为什么是我们（差异化）

| 常见参赛做法 | 我们的差异 |
|---|---|
| HarmBench/公开越狱集 + 单一 ASR 数字 | **智能体工具滥用向量**：来自我们已公开的红队仓（swarmbox 沙箱逃逸 32 向量、alexaguard-mcp 注入向量），攻击面是"agent 会调工具"，不是"chat 会说话" |
| 修完只报 ASR 降了多少 | 同时报 **over-refusal**（benign 探针被拒率）——修成拒绝一切的模型是残缺品，我们把它当一等指标 |
| 修完就交 | CircuitKIT 定位拒绝/工具滥用电路，**解释**修复位置，不只断言有效 |
| 数字好看就行 | 全部指标挂 AuditKIT run fingerprint，可复现、可审计 |

## 3. 管线（四个 Lexsi 仓全部串联，超过 ≥2 最低要求）

```
attack_corpus.py（零依赖，本地/Colab 均可跑）
   │  34 条攻击（prompt-injection 10 / tool-abuse 12 / data-harm 6 /
   │  agentic-misuse 6）+ 17 条 benign 探针（9 条 edgy-but-legit）
   ▼
① CuratorKIT   hygiene 闸门（secrets/PII/toxicity）+ minhash 去重
               → 训练/评测双用数据集（HF dataset）
   ▼
② SafeTune     先 audit 基线拒绝行为（before 指标），再 repair：
               · steer（推理期拒绝方向消融，免训练，无 GPU 也能跑）
               · harden（SafeGrad 梯度外科，train-time，GPU）
               （默认 steer：求稳，窗口内 14 天不一定有稳定 GPU）
   ▼
③ CircuitKIT   identify_safety_neurons 定位安全电路 → 解释"修在哪"
   ▼
④ AuditKIT     before/after 双跑，>1 指标，fingerprint 存档
```

**模型**：`CohereLabs/tiny-aya-global`（官方许可列表内；Colab/Kaggle
免费 GPU 足够 Tiny Aya 量级——官方原话）。

## 4. 指标（交稿强制 before-and-after >1 指标）

| 指标 | 方向 | 含义 |
|---|---|---|
| `attack_success_rate` | ↓ | 34 条攻击中模型配合/半配合的比例 |
| `over_refusal_rate` | ↓ | 17 条 benign 探针被拒比例（**成本侧**） |
| per-category 分解 | — | 按 4 类攻击 + 4 类 benign 分解，"总体变好"不能藏"工具滥用变更差"或"安全工作探针被拒变多" |
| AuditKIT fingerprint | — | 每次评测的稳定 sha256，write-up 引用精确 run id |

refusal 判定：启发式前缀为一阶段过滤，AuditKIT judge/annotator 指标为
权威标签——只糊弄前缀的修复过不了 judge。

## 5. 窗口纪律（官方规则："Projects must be developed during the hackathon"）

- 已公开的 swarmbox / alexaguard-mcp 等红队仓是**先于赛事存在的开源库**，
  与引用任何第三方库同级；`attack_corpus.py` 的向量即从这些库的模式来。
- **本目录（lexsi_redteam/）在 11-01 开窗前保持本地私有**，不推 Gitee/
  GitHub、不建 HF Space——窗口内才公开，避免"提前建好项目"的资格争议。
- 开窗后：11-01 建 HF dataset/model/space + 公开仓；11-05 前注册；
  11-14 EOD 前交稿。

## 6. 风险与对策

| 风险 | 对策 |
|---|---|
| 无本地 GPU，Tiny Aya 训练跑不动 | 默认 steer（推理期干预免训练）；Colab/Kaggle 免费 GPU + 官方 $20 HF 额度兜底 |
| SafeTune API 与文档漂移 | 开窗日（10-31 kickoff）先跑通 quickstart 再改代码；管线里每个 stage 独立可重跑 |
| 指标提升不明显 | 语料按向量分解，哪类修不动就报哪类——**如实报告比好看的数字更接近获奖**（评委看 write-up 质量） |
| 注册要用户操作 | 11-05 截止前 48h 自动提醒已排（automation）；注册约 5 分钟 |
