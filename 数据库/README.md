# Biosignature Molecular Database

本文件说明 `数据库` 文件夹中当前研究数据库的目的、结构、版本关系和后续使用规则。

当前推荐使用的主数据库文件是：

`5.20完善数据集_research_db_v0.5_workflow_enhanced.xlsx`

原始数据文件 `5.20完善数据集.xlsx` 已保留，不应直接覆盖。后续分析、人工校验、论文制图和机器学习数据导出应优先基于 `v0.5_workflow_enhanced` 版本。

修正后的原始数据副本是：

`5.20完善数据集_ma_corrected.xlsx`

其中原始 `All` sheet 中黄色荧光标记、且由研究者说明为随机填入的 MA 值已被清空。

## 1. 科学目的

本数据库服务于两个连续的研究目标。

第一，检验 Molecular Assembly Index, MA 作为生命判据的局限性。当前研究假设不是简单地说“MA 没有价值”，而是更严格地检验：

- 高 MA 不等于必然生命来源：寻找明确非生物成因但 MA 较高的分子。
- 低 MA 不等于排除生命来源：寻找明确生物来源或强生物标志物候选但 MA 较低的分子。

第二，为后续机器学习探索新的生物标志物特征建立数据基础。机器学习目标不是让模型学习研究者对 MA 的先验态度，而是建立一个有证据等级、结构来源、标签强度和泄漏控制的数据集，用于探索生物与非生物分子在结构空间中的差异。

因此，本数据库不是普通的分子属性表，而是一个证据驱动的研究数据库。每个分子条目都应尽量回答：

- 这个分子来自哪篇文献或哪个数据库？
- 原文在什么样品、实验或环境中报告了它？
- 它的结构图或 `.mol` / SMILES 来源是否可审计？
- 它的生物/非生物标签有多强？
- 它是否适合 MA 主分析、敏感性分析或机器学习训练？

## 2. 版本说明

### `5.20完善数据集.xlsx`

原始主数据集。包含 `All` 及各来源 sheet，例如 Bennu、Ryugu、Prebio、Combustion、Biomarker、Peptide 等。该文件适合追溯早期整理结果，但不包含完整证据链结构。

### `5.20完善数据集_research_db_v0.1.xlsx`

第一版研究数据库骨架。新增来源表、证据表、人工审核主表、机器学习视图和候选反例表。

### `5.20完善数据集_research_db_v0.2_pilot20.xlsx`

在 v0.1 基础上增加 20 条高质量试点样本，形成 `Pilot_Curation_20`。这些样本覆盖 Bennu、Ryugu、高 MA 非生物 PAH、低 MA biomarker 候选和 siderophore。

### `5.20完善数据集_research_db_v0.3_sources_dedup.xlsx`

在 v0.2 基础上对文献来源去重，建立规范来源表 `Source_Registry` 和别名表 `Source_Aliases`。该版本保留作来源去重前后的参考。

### `5.20完善数据集_research_db_v0.4_ma_corrected.xlsx`

历史修正版。在 v0.3 基础上删除原始表中黄色荧光标记的随机 MA 值，并新增 `MA_Removal_Log`。该版本统计：

- 原始分子记录：313 条
- 规范来源：60 条
- 重复来源 alias：12 条
- 重复来源组：11 组
- 20 条试点样本已同步标记
- 已删除随机 MA 值：51 条

### `5.20完善数据集_research_db_v0.5_workflow_enhanced.xlsx`

当前推荐版本。在 v0.4 基础上新增面向后续人工整理和计算复现的工作流 sheet。当前关键状态：

- 当前分子记录：313 条
- 当前 MA 缺失：51 条
- 已有 MA 但缺少完整算法版本/输入文件溯源：262 条
- MA 重算或溯源补全队列：313 条
- 结构审计队列：313 条
- 非生物成因检索队列：31 条
- 来源链接任务队列：293 条

## 3. 工作簿结构

### `README`

Excel 内部的简短说明 sheet。更完整说明以本 `README.md` 为准。

### `Change_Log`

数据库版本变更记录。每次结构性修改、批量数据导入、来源去重或字段规则改变都应在这里记录。

### `Data_Dictionary`

核心字段解释。用于说明各 sheet 中重要字段的含义，例如 `Origin_Class`、`Graph_Level`、`MA_Use`、`Train_Eligible` 等。

### `Curation_Priority`

当前数据库整理任务的优先级总览。它把下一步工作拆成四类：

- `P0`：重算被删除的随机 MA 值。
- `P1`：补充 `.mol` 文件路径与 SHA256。
- `P2`：链接精确来源表格、图或补充材料位置。
- `P3`：为生物候选分子检索是否存在非生物成因报告。

### `MA_Recompute_Queue`

MA 重算与溯源补全队列。包含所有需要重新计算 MA、补充 `assembly-theory` 版本、补充输入文件路径或补充运行记录的条目。

重要字段：

- `Task_Priority`：任务优先级。
- `Recommended_Input`：推荐输入文件状态。
- `MA_Implementation`：当前记录为 `DaymudeLab/assembly-theory`。
- `MA_Code_Commit`：待补的具体代码版本或 commit。
- `MA_Input_File` / `MA_Input_SHA256`：待补的输入文件与哈希。
- `Required_Action`：应执行的动作。

### `Structure_Audit_Log`

结构审计队列。用于检查每个分子是否有可审计分子图、`.mol` 文件、结构标识符和哈希值。当前大部分条目虽然有 SMILES，但还没有 `Mol_File` 与 `Mol_File_SHA256`，因此仍进入审计队列。

### `Nonbiotic_Search_Log`

生物候选分子的非生物成因检索队列。用于避免把 biomarker 或 peptide 简单当作“生命专属”标签。

### `Source_Link_Task_Queue`

来源链接任务队列。用于补齐 `Evidence_Log` 中缺失的 `Source_ID` 和 `Evidence_Location`。

### `Pilot_Curation_20`

20 条试点样本。它们不是最终训练集，而是用于测试证据链工作流的高优先级样本。

当前试点包括：

- Bennu direct-return abiotic low-MA / nucleobase / PAH 样本
- Ryugu direct-return abiotic low-MA / nucleobase / vitamin precursor 样本
- 高 MA 非生物 methane-pyrolysis PAH
- 低 MA biomarker 候选
- 低 MA biological siderophore 候选

重要原则：试点样本已被标记为 `pilot_holdout_until_full_source_audit`，在完成精确表格/补充材料定位、结构来源审计和泄漏组划分前，不应直接进入机器学习训练。

在 v0.4 中，原试点中的 4 条高 MA methane-pyrolysis PAH 的 MA 被清空，因为它们来自原始表中黄色标记的随机占位值。这些分子仍可作为重要结构候选，但不能再作为高 MA 反例，直到用可审计算法重新计算 MA。

### `MA_Removal_Log`

记录 v0.4 中被删除的随机 MA 值。每一行对应原始 `All` sheet 中一个黄色荧光标记条目。

重要字段：

- `Record_ID`：对应研究数据库中的证据记录。
- `Original_All_Row`：原始 `All` sheet 的行号。
- `Original_Name`：分子或候选结构名称。
- `Origin`：来源类别。
- `Old_MA`：被删除的原 MA 占位值。
- `Removal_Reason`：删除原因。

### `Source_Registry`

规范来源表。每一行是一篇去重后的文献、数据库、报告或其它来源。

重要字段：

- `Source_ID`：规范来源 ID，证据表应引用这个 ID。
- `BibTeX_Key`：该规范来源对应的主要 BibTeX key。
- `Title`, `Authors`, `Year`, `Journal`, `DOI_or_URL`：文献信息。
- `Source_Domain`：来源领域，如 Bennu、Ryugu、Combustion、Assembly_Theory、Natural_Product 等。
- `Duplicate_Count`：该规范来源合并了多少个原始 BibTeX 条目。
- `Alias_Source_IDs`：被合并到该来源的原始 Source_ID。
- `Alias_BibTeX_Keys`：被合并的 BibTeX key。

注意：`Source_Registry` 中只保留 canonical source；重复来源不删除，而是进入 `Source_Aliases`。

### `Source_Aliases`

来源别名表。用于保留原始 BibTeX 条目的完整痕迹。

重要字段：

- `Original_Source_ID`：原始来源 ID。
- `Canonical_Source_ID`：去重后应引用的规范来源 ID。
- `Duplicate_Group`：重复组 ID。
- `Alias_Status`：`canonical` 或 `duplicate_alias`。

这个 sheet 的作用是防止同一篇论文在数据库中被当成多篇独立来源。

### `Evidence_Log`

证据记录表。每一行表示“从某个来源抽取到的一条分子证据”，而不是一个唯一分子。

重要字段：

- `Record_ID`：证据记录 ID。
- `Source_ID`：对应 `Source_Registry` 的规范来源。
- `Original_Name_in_Source`：原文或原始表中的名称。
- `Reported_Formula`：原始报告中的分子式。
- `Reported_SMILES_or_Structure`：原始或迁移得到的结构信息。
- `Reported_Context`：样品、实验或来源环境。
- `Reported_Origin_Label`：该证据支持的生物/非生物标签。
- `Evidence_Location`：具体表格、图、页码、补充材料或数据库位置。
- `Extraction_Method`：人工抽取、数据库导出、legacy Excel 迁移等。
- `Evidence_Confidence`：该证据的当前可信度。

这是后续最需要人工补强的 sheet。论文中每一个关键反例都应能回到这里。

### `Molecule_Curated`

人工审核主表。每一行是一个当前用于分析的分子条目，来自原始 `All` 表迁移，并加入证据等级、结构等级和分析用途。

重要字段：

- `Curated_ID`：分子条目 ID。
- `Record_IDs`：对应的证据记录。
- `Preferred_Name`：推荐名称。
- `Canonical_SMILES`, `Isomeric_SMILES`, `InChIKey`, `CID`：结构标识。
- `Origin_Class`：更细的来源分类。
- `Origin_Label`：`biological` / `abiotic` / `ambiguous`。
- `Label_Strength`：`strong` / `candidate_strong` / `weak` / `unchecked` 等。
- `Label_Basis`：标签依据，如 returned asteroid sample、biomarker literature claim、abiotic process context。
- `Nonbiotic_Report_Status`：对生物候选分子，记录是否已检索非生物成因报告。
- `Graph_Level`：分子图确定程度。
- `Graph_Ambiguity`：结构歧义类型。
- `MA_Use`：是否进入 MA 主分析或反例候选。
- `Source_Validation_Status`：来源验证状态。
- `Confidence`：当前综合置信度。
- `Conflict_Flag`：重复 InChIKey 或多来源提示。
- `Curation_Note`：人工审核备注。

### `ML_Dataset_View`

机器学习视图。该 sheet 面向未来模型训练，但当前不应被直接当作最终训练集。

重要字段：

- `Canonical_SMILES`, `Isomeric_SMILES`, `InChIKey`：结构输入。
- `Origin_Label`, `Label_Strength`, `Label_Basis`：标签信息。
- `Source_Domain`：来源域，用于评估模型是否只学到来源偏差。
- `MA`：当前 MA 值。
- `Graph_Level`, `Graph_Ambiguity`：结构质量。
- `Train_Eligible`：训练可用性状态。
- `Reason_Excluded`：排除或暂缓训练原因。
- `Leakage_Group`：初步泄漏控制组，目前使用 InChIKey 前段，未来应替换或补充 scaffold 分组。

当前 20 条试点样本被标记为 holdout，避免在证据链未完全审核前进入训练。

### `High_MA_Abiotic`

自动筛选出的高 MA 非生物候选反例。当前规则为：

`Origin_Label = abiotic` 且 `MA >= 15`

这些条目服务于论点：“高 MA 不等于生命来源”。但其中部分条目仍需完成精确来源定位和结构来源审计。

注意：v0.4 删除黄色标记随机 MA 后，高 MA 非生物候选表发生变化。被删除 MA 的 PAH 候选不再进入该表，直到重新计算。

### `Low_MA_Bio`

自动筛选出的低 MA 生物或生物候选反例。当前规则为：

`Origin_Label = biological` 且 `MA < 15`

这些条目服务于论点：“低 MA 不等于排除生命来源”。注意 biomarker 候选不应直接宣称生命专属，必须通过 targeted nonbiotic search 检查是否存在可靠非生物成因报告。

### `Curation_Checks`

质量检查汇总。包括行数、缺失结构字段、高 MA 非生物候选数、低 MA 生物候选数、来源去重结果等。

### `Legacy_*`

原始 workbook 中各 sheet 的保留副本，例如：

- `Legacy_All`
- `Legacy_Bennu`
- `Legacy_Ryugu`
- `Legacy_Prebio`
- `Legacy_Combustion`
- `Legacy_Biomarker`
- `Legacy_Peptide`

这些 sheet 不应作为主要分析入口，但可用于追溯早期整理过程。

## 4. 核心分类规则

### 来源分类：`Origin_Class`

当前主要类别包括：

- `Abiotic_Strong`：明确非生物来源或非生物环境，例如 Bennu、Ryugu、Prebio、Combustion。
- `Bio_Strong_Candidate`：强生物候选，如 biomarker、siderophore，但仍需非生物成因检索。
- `Bio_Ambiguous`：生物相关但不适合作为强标签，例如一般 peptide 类条目。
- `Unclassified`：尚未分类。

### 结构等级：`Graph_Level`

MA 计算不要求 PubChem CID，但要求可审计的分子图。当前分级思想为：

- `G0_no_single_molecular_graph`：没有单一明确分子图。
- `G1_formula_only_or_unresolved`：只有分子式或结构不唯一。
- `G3_2D_graph_available`：有明确二维结构图，可由 SMILES 或 `.mol` 表示。

未来如加入 `.mol` 文件，可继续扩展到：

- `G2_candidate_graph`
- `G4_stereochemical_graph`

### MA 使用状态：`MA_Use`

- `MA_primary`：可进入 MA 主分析。
- `MA_primary_counterexample_candidate`：高 MA 非生物反例候选。
- `MA_sensitivity_or_requires_mol_audit`：可用于敏感性分析，但需要结构来源审计。
- `exclude_until_MA_computed`：暂不进入 MA 分析。

### 机器学习使用状态：`Train_Eligible`

当前数据库对机器学习保持保守：

- `provisional_after_source_verification`：来源验证完成后可考虑训练。
- `pilot_holdout_until_full_source_audit`：试点样本，暂不训练。
- `no`：当前不适合训练。

## 5. 推荐工作流

### Step 1: 新增文献或数据库来源

先加入 `Assembly Method.bib`，然后运行来源解析和去重脚本。不要直接把分子塞进主表。

### Step 2: 在 `Source_Registry` 中确认来源

检查是否已有同 DOI 或同标题来源。如有重复，应保留 canonical source，并在 `Source_Aliases` 中记录 alias。

### Step 3: 在 `Evidence_Log` 中记录证据

每条证据应尽量补齐：

- `Source_ID`
- `Original_Name_in_Source`
- `Evidence_Location`
- `Reported_Context`
- `Extraction_Method`
- `Evidence_Confidence`

### Step 4: 在 `Molecule_Curated` 中审核结构和标签

判断：

- 是否有可审计分子图？
- 是否可算 MA？
- 标签是强、候选强、弱还是未检查？
- 是否存在同一 InChIKey 多来源？

### Step 5: 更新 MA 与复杂度指标

MA、BertzCT、Shannon Entropy、LMC 等指标应记录计算方法、算法版本和异常情况。当前版本还未系统加入算法版本字段，后续应补。

当前 MA 计算基于开源项目：

`DaymudeLab/assembly-theory`

项目地址：

`https://github.com/DaymudeLab/assembly-theory`

该项目 README 说明其为用于计算 molecular structures 的 assembly indices 的开源高性能库，Rust 实现，同时提供 Python package 和命令行工具；命令行工具可以直接接受 `.mol` 文件作为输入计算 assembly index。

后续数据库版本应增加或补齐以下 MA 计算溯源字段：

- `MA_Method`
- `MA_Implementation`
- `MA_Code_URL`
- `MA_Code_Commit`
- `MA_Input_File`
- `MA_Input_SHA256`
- `MA_Run_Date`
- `MA_Status`
- `MA_Runtime_or_Timeout`
- `MA_Error_or_Note`

这一步非常重要，因为 MA 的精确计算对大分子可能非常困难。没有算法版本、输入文件和运行状态的 MA 数值，不应作为论文中的强证据。

### Step 6: 生成 ML 视图

机器学习训练集不应手工随意筛。应从 `Molecule_Curated` 生成，且至少满足：

- 明确分子图
- 强标签
- 来源已验证
- 去重和泄漏组已处理
- ambiguous/context-only 条目不进入主训练

## 6. 当前已知限制

1. 大多数 legacy 数据仍缺少精确 `Evidence_Location`。

2. 45 条记录缺少 `Canonical_SMILES` / `InChIKey` 等结构字段，但其中一些已有 MA。这说明它们可能来自 `.mol` 或其它结构来源，需要补充 `Mol_File` 和 `Mol_File_SHA256`。

3. 当前已知 MA 计算基于 `DaymudeLab/assembly-theory`，但旧表中尚未逐行记录该代码的具体版本或 commit。后续必须补充 MA 计算溯源字段。

4. Biomarker 条目目前是“生物候选”，不是“生命专属分子”。必须进行 targeted nonbiotic search。

5. Ryugu 中 uracil / vitamin B3 等条目需要补充更具体的一手来源。当前主要接到 `Naraoka et al. 2023` 来源域，后续应加入专门的 Ryugu nucleobase/vitamin 文献。

6. Methane pyrolysis 高 MA PAH 目前多为 process/model group-level evidence。正式论文中需要把具体分子名与机制物种标签精确对应。

7. `ML_Dataset_View` 只是候选视图，不能直接作为最终训练集。

## 7. 脚本

相关脚本位于项目根目录的 `scripts` 文件夹。

### `scripts/build_research_database.py`

从原始 `5.20完善数据集.xlsx` 和 `Assembly Method.bib` 构建 v0.1 研究数据库骨架。

### `scripts/curate_pilot_20.py`

在 v0.1 基础上生成 v0.2，加入 20 条试点样本，并同步更新 `Evidence_Log`、`Molecule_Curated` 和 `ML_Dataset_View`。

### `scripts/deduplicate_sources.py`

在 v0.2 基础上生成 v0.3，按规范 DOI 和标题合并重复来源，新增 `Source_Aliases`，并更新相关 `Source_ID` 引用。

### `scripts/remove_highlighted_random_ma.py`

在 v0.3 基础上生成 v0.4，读取原始 `5.20完善数据集.xlsx` 中黄色荧光标记的行，删除这些随机填入的 MA 值，生成 `MA_Removal_Log`，并重新生成高/低 MA 候选视图。

### `scripts/enhance_database_v0_5.py`

在 v0.4 基础上生成 v0.5，新增 MA 计算溯源字段，并生成 `Curation_Priority`、`MA_Recompute_Queue`、`Structure_Audit_Log`、`Nonbiotic_Search_Log` 和 `Source_Link_Task_Queue`。

## 8. 使用原则

1. 不要把 PubChem 是否存在作为唯一准入标准。真正的准入标准是是否有可审计的分子图。

2. 不要把同一个分子的多来源记录简单删除。它们是证据，不是噪音。

3. 不要把 `Origin = biological` 直接等同于“生命专属”。生物候选必须检查非生物成因报告。

4. 不要在来源未验证前训练机器学习模型。否则模型可能学习到数据库偏差或研究者先验。

5. 所有强结论都应能回到 `Source_Registry` 和 `Evidence_Log`。

## 9. 下一步优先任务

优先完成 `Pilot_Curation_20` 的 A 级证据链：

- 为每条试点样本补充具体 table / figure / supplementary location。
- 为 biomarker 候选建立非生物成因检索日志。
- 为缺少结构字段但已有 MA 的条目补充 `.mol` 文件路径和 SHA256。
- 为高 MA PAH 建立具体分子名与 methane pyrolysis mechanism species 的对应表。
- 重新计算 `MA_Removal_Log` 中 51 条分子的 MA，尤其是 A1-A44、Microcystin-lr、Nodularin R 和大型 PAH。
- 为所有 MA 值补充 `DaymudeLab/assembly-theory` 的具体代码版本或 commit，以及输入 `.mol` 文件哈希。
- 为 ML 训练准备 scaffold / leakage group，而不是只依赖 InChIKey 前段。
