# Dev Diagnosis.skill

開發需求分診與治理引導工具。

從模糊的需求出發，經過結構化診斷，判斷該用什麼方式開發、如何管理。

## 流程

```mermaid
flowchart TD
    Start([開發需求]) --> Station{棧點設定檔存在？}
    Station -->|否| CreateStation[引導建立棧點設定檔]
    CreateStation --> FastTrack
    Station -->|是| FastTrack
    FastTrack{使用者已經想清楚了？}
    FastTrack -->|是| QuickReport([直接整理分診報告 → 確認])
    FastTrack -->|否| Entry

    subgraph P1 ["階段一：診斷"]
        Entry{新需求 or 現有工具的問題？}
        Entry -->|新需求| Ask[釐清問題與痛點]
        Entry -->|現有工具的問題| SkillReview

        Ask --> SkillReview{既有 skill 清單}
        SkillReview -->|清單是空的| ColdStart[跳過復盤]
        SkillReview -->|清單有東西| Review[復盤 + 交織性分析]

        Review --> ReviewResult{復盤結論}
        ReviewResult -->|直接用| Done([結束])
        ReviewResult -->|可改造| Refactor[確認改造範圍]
        ReviewResult -->|可串接| Gap[補缺口]
        ReviewResult -->|重建| Extend

        Refactor --> Extend
        Gap --> Extend
        ColdStart --> Extend

        Extend[延展性評估] --> DiagOutput[/診斷摘要 → 使用者確認/]
    end

    subgraph P2 ["階段二：選解法"]
        Auto{需要自動化？}
        Auto -->|否| Manual([維持人工 → 記錄決策])
        Auto -->|是| Degree[自動化程度期待？]

        Degree --> Rule{規則能寫死？}
        Rule -->|是，且期待低| Script[自動化腳本]
        Rule -->|是，但期待高| Multi
        Rule -->|否| Multi{需要多步驟自主決策？}
        Multi -->|否| AITool[AI 工具]
        Multi -->|是| Agent[Agent]

        Script --> ExtendFix
        AITool --> ExtendFix
        Agent --> ExtendFix

        ExtendFix{延展性高？}
        ExtendFix -->|是| Upgrade[建議往上推一層 → 使用者決定]
        ExtendFix -->|否| SolveOutput
        Upgrade --> SolveOutput

        SolveOutput[/解法摘要 → 使用者確認/]
    end

    subgraph P3 ["階段三：分級管理"]
        Tech{"持久化 / 對外接口 / 多人 / 部署？"}
        Tech -->|任一為重| Managed[受管開發]
        Tech -->|有輕無重| Light[輕量管理]
        Tech -->|皆無| Free[自由開發]

        Managed --> LevelOutput
        Light --> LevelOutput
        Free --> LevelOutput

        LevelOutput[/分級摘要 → 使用者確認/]
    end

    subgraph P4 ["階段四：落地"]
        SkillQ{值得寫成 skill？}
        SkillQ -->|是| WriteSkill[建立 skill 結構]
        SkillQ -->|否| Register
        WriteSkill --> Place{管理層級}
        Place -->|受管| Repo[進 GitHub repo]
        Place -->|輕量| RepoLight[進 repo + README]
        Place -->|自由| Personal[存個人空間]
        Repo --> Register
        RepoLight --> Register
        Personal --> Register
        Register[登記到既有 skill 清單] --> NextStep[具體下一步行動]
        NextStep --> Report([產出分診報告])
    end

    DiagOutput --> Auto
    SolveOutput --> Tech
    LevelOutput --> SkillQ
```

## 棧點（Station）

棧點是你的工作環境——一個組織、一個團隊、或一個專案。每個棧點有自己的技術棧、資安限制、開發規範和既有工具清單。

分診流程會根據你所在的棧點，套用對應的規則來判斷。例如同樣一個需求，在資安要求嚴格的組織可能走受管開發，在個人專案裡可能是自由開發。

用 `_template.md` 為你的棧點建立設定檔，放在 `stations/` 目錄下。

## 在哪裡使用

這個 skill 可以在 Claude.ai 或 Claude Code 中使用，但能做的事不同：

| | Claude.ai | Claude Code |
|---|---|---|
| 對話式分診引導 | v | v |
| 自動讀取棧點設定檔 | x（需手動貼入） | v |
| 建立檔案結構、寫 SKILL.md | x | v |
| 登記到既有 skill 清單 | x | v |
| 建 repo、操作 git | x | v |
| 串接 MCP 工具 | x | v |

簡單說：Claude.ai 能「診斷」，Claude Code 能「診斷 + 落地」。

## 檔案

- `SKILL.md` — 完整的分診引導邏輯，可作為 Claude skill 使用
- `_template.md` — 棧點設定檔模板

## 使用方式

1. 將 `SKILL.md` 加入 Claude 的 skill 設定
2. 當有開發需求時，觸發此 skill 進行分診
3. 首次使用會先引導你建立棧點設定檔，之後直接進入分診
