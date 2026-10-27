# 提交清单 — Lexsi AI Safety Hackathon（Track 2）

> 硬截止：**交稿 11-14 EOD**｜注册 11-05 截止｜Demo Day 11-22
> AI 侧已完工：语料生成器 + 35 条断言 + dry-run 全链路（mock 模型验证测量管线）

## 用户动作（共约 30 分钟，分三天）

| # | 动作 | 时间 | 状态 |
|---|---|---|---|
| 1 | hackathon.lexsi.ai 注册（1 人队，免费） | 11-05 前，建议 11-01 开窗当天 | ⬜ |
| 2 | 进 Discord（discord.gg/dtEDQ2Z3eg）领 $20 HF 额度 | 注册后立即 | ⬜ |
| 3 | 确认 HF 账号可用（领额度要它） | 同步 | ⬜ |
| 4 | （可选）Kaggle/Colab 账号用于免费 GPU | 11-01 前 | ⬜ |

## 窗口内执行表（AI 侧，我已备好代码，开窗即跑）

| 阶段 | 动作 | 产出 |
|---|---|---|
| 10-31 kickoff | 看官方介绍，核对 SafeTune/CuratorKIT API 与本地假设 | 修正 notes |
| 11-01 | `attack_corpus.py` 生成语料 → CuratorKIT hygiene 跑一遍 → 传 HF dataset | HF dataset |
| 11-01~03 | AuditKIT **before** 跑 Tiny Aya Global（Colab T4 足够） | before 指标 + fingerprint |
| 11-03~07 | SafeTune steer（默认）或 harden 修复 | 修复后模型/adapter → HF |
| 11-07~10 | CircuitKIT identify_safety_neurons → 电路定位报告 | 解释性证据 |
| 11-10~12 | AuditKIT **after** + per-category 分解 + write-up | metrics.json + 文章 |
| 11-13 | 公开仓 + HF Space（demo）+ HF Article | 提交物 |
| 11-14 EOD | 提交表单 | ✅ |

## 提交物清单（官方要求）

- [ ] 公开部署（仓链接 / demo）— 我们的 Gitee 公开仓 + HF Space
- [ ] before-and-after 指标（>1）— `results/metrics.json` 的 ASR + over-refusal
- [ ] Write-up：指标选择理由、数据集/任务/模型选择理由、"如何变得更好"
- [ ] HF Article 或 HF Space 承载 write-up
- [ ] （ preferred）ROI 对比：闭源 API vs 自托管小模型的成本/吞吐

## 提交表单预填（开窗后按官方表单校对）

- 项目名：**RedTeamRepair — agentic-attack-driven safety repair for Tiny Aya**
- 赛道：Track 2 AI Red Teaming, Safety and Alignment
- 队：solo
- 串联仓：CuratorKIT + SafeTune + CircuitKIT + AuditKIT（4/4）
- 模型：CohereLabs/tiny-aya-global（官方许可列表）
- 指标：attack_success_rate ↓、over_refusal_rate 持平 ↓、per-category 分解

## 铁律（本项目内）

- 宣称=实测：write-up 里每个数字必须能在 `results/metrics.json` 或
  `eval.py` 输出里找到；dry-run 的 mock 数字**只能**标为"plumbing-verified"，
  不得当作模型结果。
- 开窗前不公开任何本项目文件（见 DESIGN.md §5）。
- 修复若在某类攻击上无效，如实报告并给出 CircuitKIT 的定位解释——
  不调语料去凑数字（那是 overfit，评委看得出）。
