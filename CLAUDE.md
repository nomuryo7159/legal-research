# legal-research

法務相談事項のリーガルリサーチを行うプロジェクト。

- リサーチ依頼（相談文＋参照資料）を受けたら **`legal-research-flow` スキル**を使う
  （4段階のサブエージェント委任パイプライン。手順はスキル側に記載）。
- サブエージェント定義: `.claude/agents/`（fact-structuring / issue-spotting /
  source-research / legal-analysis）
- 成果物の出力先: `output/`（ルート直下に置かない）
