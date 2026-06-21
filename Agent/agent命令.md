# agent命令

## 现在完整跑前 20 条 baseline 和 agent 对比，推荐按这个顺序：

**1\. 跑 Agent 推理**
```text
    uv run python scripts/run_infer.py --limit 5 --experiment agent_5_unified --no-resume --max-workers 5
```

**2\. 跑 Agent 统一评测**
```text
    uv run python scripts/run_eval.py --predictions outputs/agent_5_unified/predictions.jsonl --experiment agent_5_unified --limit 5 --max-workers 5
```

​  


**3\. 跑 baseline 原始生成与 qwen_eval 原始评估**
```text
    uv run python qwen_eval.py --limit 5 --output-dir outputs/baseline_5_unified --resume --max-workers 5
```

uv run python qwen_eval.py --output-dir outputs/baseline_full_v4 --max-workers 5 --request-timeout 240 --max-retries 3

​  


**4\. 把 baseline 转成统一评分口径**
```text
    uv run python scripts/run_baseline.py --baseline-results outputs/baseline_5_unified/evaluation_results.jsonl --experiment baseline_5_unified --limit 5 --max-workers 5
```

  


  


跑完整个数据集推理：
```text
    uv run python scripts/run_infer.py --experiment agent_full --no-resume
```

跑完整个数据集评测：
```text
    uv run python scripts/run_eval.py --predictions outputs/agent_full/predictions.jsonl --experiment agent_full
```

推理 + 评测一条命令跑完整实验：
```text
    uv run python scripts/run_experiment.py --experiment agent_full
```

如果你想强制重新跑、不复用已有输出，用：
```text
    uv run python scripts/run_experiment.py --experiment agent_full --no-resume
```

  


  


uv run python qwen_eval.py --output-dir outputs/baseline_full_v4 --no-resume --max-workers 5

uv run python scripts/run_baseline.py \

\--baseline-results outputs/baseline_full_v4/predictions.jsonl \

\--experiment baseline_full_v4 \

\--max-workers 5

​  


​  


uv run python scripts/compare_runs.py --agent outputs/agent_full_v4/eval_results.jsonl --baseline outputs/baseline_full_v4/baseline_eval_results.jsonl --output outputs/full_v4_compare.json
