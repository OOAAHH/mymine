---
layout: post
title: "HowtoevoluteRNA-ligand"
categories: think
---

考虑以下四个问题
1. **RNA-ligand 结合的物理化学基础**——研究 RNA-ligand 结合是否主要受极性键驱动，以及其他重要的相互作用。
2. **RNA-ligand 结合的结构特征**——RNA 是否预先形成 binding pocket，或 ligand 是否参与 pocket 的形成。
3. **使用 contact 作为相似性度量的合理性**——现有研究如何定义 contact cutoff（3.5Å）和角度（64°），以及 contact-based 评估方法的科学性。
4. **RNA-ligand 结合的评估标准**——RNA-ligand binding 的 benchmark 数据集和已有评分标准。

**1. RNA-小分子配体结合的物理化学机制：** RNA与配体的结合主要由极性相互作用（如氢键）和π–π堆叠驱动。 ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=this%20analysis%20with%20previously%20determined,of%20small%20molecules%20targeting%20RNA)) ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=small%20molecule%E2%80%93protein%20contacts%20%28Fig,respectively%29.%20Hydrophobic%20contacts%20were))大规模统计分析表明，小分子识别RNA时，以碱基间的堆叠和氢键贡献最大（各占约30–50%），而疏水作用相对次要（~15–20%） ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=small%20molecule%E2%80%93protein%20contacts%20%28Fig,respectively%29.%20Hydrophobic%20contacts%20were)) ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=coordination%20to%20inorganic%20ions%20%281,On%20the%20other%20hand))。例如，在已报道的RNA-配体复合物中，芳香环配体常与核苷酸碱基平行堆叠，并通过氢键定位 ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=this%20analysis%20with%20previously%20determined,of%20small%20molecules%20targeting%20RNA)) ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=small%20molecule%E2%80%93protein%20contacts%20%28Fig,respectively%29.%20Hydrophobic%20contacts%20were))。正电配体与RNA阴离子骨架的静电作用（如盐桥）和阳离子–π相互作用也有贡献，但频次较低 ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=coordination%20to%20inorganic%20ions%20%281,On%20the%20other%20hand))。此外，RNA常需要金属离子（如Mg²⁺）稳定其结构，金属离子有时直接协调配体和RNA形成桥接相互作用 ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=hydrogen%20bonding%20contacts%20that%20promote,the%20inner%20sphere%20metal%20ion))。水分子则可介导RNA与配体的间接氢键联系 ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=colored%20in%20magenta,The%20ligand%20benfotiamine%20%28BTP))。**RNA-配体结合亲和力**体现上述相互作用的综合效果，实验上通常以解离常数 *K*<sub>d</sub> 或结合自由能 Δ*G* 表征，可通过等温滴定量热法、荧光滴定等方法测定 ([
            BINANA 2: Characterizing Receptor/Ligand Interactions in Python and JavaScript - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC8889568/#:~:text=receptor%20recognizes%20its%20ligand%20,of%20drug%20discovery%2C%20accurately%20characterizing))。这些相互作用的形成与构型直接影响结合自由能，从而决定配体的结合强度 ([
            BINANA 2: Characterizing Receptor/Ligand Interactions in Python and JavaScript - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC8889568/#:~:text=receptor%20recognizes%20its%20ligand%20,of%20drug%20discovery%2C%20accurately%20characterizing))。

**2. RNA-配体结合的结构特征：** 有些RNA在配体结合前即存在预构造的结合囊袋，但也有许多RNA需要配体诱导才能形成完整的结合位点。研究表明，RNA常以“构象选择”或“诱导契合”的机制与配体结合：即RNA本身存在多种构象，配体倾向结合其中特定的稀有构象（称为**构象读取**机制） ([
            Conformational readout of RNA by small ligands - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC4111737/#:~:text=binding%20pockets%2C%20mainly%20the%20adaptive,general%20way%20by%20which%20RNA)) ([
            Conformational readout of RNA by small ligands - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC4111737/#:~:text=suggest%20that%20the%20unique%20conformations,ensemble%20of%20different%20RNA%20states))；或者配体结合后引发RNA局部构象变化以形成囊袋（诱导契合） ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=and%20ions,isolated%20red%20dots%20denote%20the))。例如，鸟嘌呤核开关（guanine riboswitch）在有配体时其适配体域内部形成稳定的配体结合口袋，而无配体时该口袋不存在，这是配体参与构建结合位点的典型案例 ([
            Pausing guides RNA folding to populate transiently stable RNA structures for riboswitch-based transcription regulation - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC5459577/#:~:text=helix%20,structure%20of%20a%20truncated%20stably))。再如 PreQ<sub>1</sub> 核开关的配体结合会挤走原位的一个腺嘌呤碱基并引起局部结构重排，从而产生配体嵌合的腔穴 ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=and%20ions,isolated%20red%20dots%20denote%20the))。也有RNA在未结合配体时就保持配体口袋的构型（所谓非适应性复合物），配体直接进入预先形成的空腔结合 ([
            Conformational readout of RNA by small ligands - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC4111737/#:~:text=binding%20pockets%2C%20mainly%20the%20adaptive,general%20way%20by%20which%20RNA)) ([
            Conformational readout of RNA by small ligands - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC4111737/#:~:text=suggest%20that%20the%20unique%20conformations,ensemble%20of%20different%20RNA%20states))。**预测结构与实验结构的偏差定义：** 计算和实验中通常用构象比较指标量化预测模型与实际结构的差异，最常用的是配体的根均方偏差（RMSD）。例如，将对接预测的配体位置与晶体复合物中的配体相比，计算重原子RMSD以评估偏离程度，RMSD越小表示预测构型越接近真实结合方式 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=match%20at%20L859%20This%20observation,see))。一般而言，RMSD ≤2 Å常被视为成功重现了实验构型的“近本底”姿态标准。

**3. 基于接触（contact）的相似性度量：** 使用配体-受体原子接触模式来评估RNA-配体构象相似性是当前研究的有效方法之一。以接触为基础的指纹（fingerprint）描述能捕捉关键相互作用，有助于辨别“功能等价”的结合姿态，即使整体RMSD存在差异 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=including%20analysis%20of%20interactions%20formed,szulc%2FfingeRNAt)) ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=fingeRNAt%20finds%20application%20in%20multiple,program%20can%20be%20used%20for))。在RNA-配体系中，不同结合姿势的RMSD与其相互作用模式相似度往往相关性很弱 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=match%20at%20L859%20This%20observation,see))——这表明仅凭几何RMSD可能无法判断配体是否形成了正确的相互作用网络。相较之下，接触指纹能更直接地反映氢键、堆叠等作用是否再现 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=including%20analysis%20of%20interactions%20formed,szulc%2FfingeRNAt)) ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=fingeRNAt%20finds%20application%20in%20multiple,program%20can%20be%20used%20for))。**接触判据的定义：** 文献中常采用经验阈值来定义配体-RNA接触，例如重原子距离在3.5 Å以内通常视为发生直接接触（包括氢键和范德华接触） ([
            Role of salt-bridging interactions in recognition of viral RNA by arginine-rich peptides - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC8633718/#:~:text=atom%20of%20the%20phosphate%20group,atoms%20presented%20in%20Table%20S2))。这一 cutoff (~3.5 Å) 源于统计大量复合物中原子接近的典型距离，也是氢键键长的合理上限 ([
            Role of salt-bridging interactions in recognition of viral RNA by arginine-rich peptides - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC8633718/#:~:text=atom%20of%20the%20phosphate%20group,atoms%20presented%20in%20Table%20S2))。同时，为确保接触具有正确的空间取向，常结合角度判据：例如定义氢键时要求供体-受体的键角>120–150°以保证线性度；定义芳香环 π–π 堆叠时要求两环平面法线夹角在一定阈值内（例如小于约30°表示平行堆叠，而接近90°则为垂直*T*形堆叠） ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=aliphatic%20and%20aromatic%20carbons%20within,when%20ligand%20positively%20charged%20atoms))。这些阈值通常依据已知结构统计和物理有机化学原理设定。**接触法与其他评估的对比：** 基于接触的评分方法在评价RNA-配体结合时表现出良好的一致性和判别力，在某些情况下可优于单纯的RMSD判据 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=including%20analysis%20of%20interactions%20formed,szulc%2FfingeRNAt)) ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=fingeRNAt%20finds%20application%20in%20multiple,program%20can%20be%20used%20for))。RMSD度量的是几何位置偏差，可能忽略配体内部对称性或RNA整体刚体移动等因素，而接触指纹关注具体相互作用是否再现，因而更能反映“生物学上正确”的结合模式 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=match%20at%20L859%20This%20observation,see))。至于结合自由能或相互作用能的计算评估，它们在理论上直接对应结合亲和力，但要求对溶剂、离子和构象变化进行准确建模，计算成本高且在RNA体系中精度受限。因此，在实际评估中，会结合多种指标：接触模式用于定性比较相互作用网络，RMSD用于定量衡量构象差异，结合能计算提供热力学依据，综合判断配体结合构型的合理性。

**4. RNA-配体结合的评估标准：** **评分标准和基准**：目前尚无像蛋白-配体那样统一的RNA-配体打分函数标准，但研究者已提出一些专门的评估体系和数据集用于方法验证。例如，Fan等建立了RNA-Ligand Interaction Database (RNALID)，收集了358个经实验证实的RNA-小分子相互作用及其结合亲和力数据，可作为RNA-配体结合研究的基准资源 ([Characterizing RNA-binding ligands on structures, chemical information, binding affinity and drug-likeness - PubMed](https://pubmed.ncbi.nlm.nih.gov/37415294/#:~:text=comprehensively%2C%20especially%20in%20the%20binding,ligand))。模型预测中，经常借鉴蛋白-配体领域的指标：对于对接构象预测，以配体重原子RMSD ≤2 Å作为成功重现晶体姿态的标准；对于亲和力预测，则比较计算打分与实验 *K*<sub>d</sub>/*ΔG* 的相关性来评估评分函数性能 ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=match%20at%20L859%20This%20observation,see)) ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=rely%20on%20scoring%20the%20possible,the%20predicted%20scores%20and%20the))。近年来也出现了针对RNA的打分方案改进，例如调整AutoDock等评分函数参数以更好地适应RNA-配体体系 ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=match%20at%20L665%20For%20example%2C,The%20accuracy%20may%20be%20further))。**RNA-配体结合口袋的定义：** 在已知复合物结构中，通常将距离配体一定范围内的核苷酸定义为“结合口袋”区域（例如所有与配体任一原子距离在4 Å以内的碱基和糖-磷酸骨架残基） ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=hydrogen%20bonding%20contacts%20that%20promote,the%20inner%20sphere%20metal%20ion))。实验上，这些区域可通过晶体学定位配体邻近的RNA原子来确定，或通过化学探针实验证实配体结合导致某些核苷酸保护/变化，从而界定结合位点范围。在计算预测中，常借助**口袋识别算法**（如Pocket-Finder等）从RNA结构表面找到潜在的小分子结合腔穴，并评估其体积、深度等“口袋质量”指标 ([Principles for targeting RNA with drug-like small molecules - PubMed](https://pubmed.ncbi.nlm.nih.gov/29977051/#:~:text=Structures%20are%20coloured%20by%20pocket,Pocket%20for%20linezolid%20in%20the)) ([Principles for targeting RNA with drug-like small molecules - PubMed](https://pubmed.ncbi.nlm.nih.gov/29977051/#:~:text=retrovirus%20type%201%20%28SRV,PowerPoint%20slide))。例如，有研究按照口袋的尺寸和埋藏程度对RNA结构进行着色评分，以判定哪些结构域具备良好的配体结合腔 ([Principles for targeting RNA with drug-like small molecules - PubMed](https://pubmed.ncbi.nlm.nih.gov/29977051/#:~:text=Structures%20are%20coloured%20by%20pocket,Pocket%20for%20linezolid%20in%20the)) ([Principles for targeting RNA with drug-like small molecules - PubMed](https://pubmed.ncbi.nlm.nih.gov/29977051/#:~:text=retrovirus%20type%201%20%28SRV,PowerPoint%20slide))。综合而言，“结合口袋区域”可在实验结构中明确为包围配体的那组RNA碱基和骨架片段，在计算模型中则通过空间几何和能量判据预测并定义，用于后续结合评分和功能分析。 

**参考文献：**

2.  ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=this%20analysis%20with%20previously%20determined,of%20small%20molecules%20targeting%20RNA)) ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=small%20molecule%E2%80%93protein%20contacts%20%28Fig,respectively%29.%20Hydrophobic%20contacts%20were));  ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=coordination%20to%20inorganic%20ions%20%281,On%20the%20other%20hand))

3.  ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=hydrogen%20bonding%20contacts%20that%20promote,the%20inner%20sphere%20metal%20ion)) ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=colored%20in%20magenta,The%20ligand%20benfotiamine%20%28BTP))

4.  ([
            BINANA 2: Characterizing Receptor/Ligand Interactions in Python and JavaScript - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC8889568/#:~:text=receptor%20recognizes%20its%20ligand%20,of%20drug%20discovery%2C%20accurately%20characterizing))

5.  ([
            Conformational readout of RNA by small ligands - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC4111737/#:~:text=binding%20pockets%2C%20mainly%20the%20adaptive,general%20way%20by%20which%20RNA)) ([
            Conformational readout of RNA by small ligands - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC4111737/#:~:text=suggest%20that%20the%20unique%20conformations,ensemble%20of%20different%20RNA%20states))

6.  ([
            Pausing guides RNA folding to populate transiently stable RNA structures for riboswitch-based transcription regulation - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC5459577/#:~:text=helix%20,structure%20of%20a%20truncated%20stably))

7.  ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=and%20ions,isolated%20red%20dots%20denote%20the))

8.  ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=match%20at%20L859%20This%20observation,see))

9.  ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=including%20analysis%20of%20interactions%20formed,szulc%2FfingeRNAt)) ([
            fingeRNAt—A novel tool for high-throughput analysis of nucleic acid-ligand interactions - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC9197077/#:~:text=fingeRNAt%20finds%20application%20in%20multiple,program%20can%20be%20used%20for))

10.  ([
            Role of salt-bridging interactions in recognition of viral RNA by arginine-rich peptides - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC8633718/#:~:text=atom%20of%20the%20phosphate%20group,atoms%20presented%20in%20Table%20S2))

11.  ([
            Systematic analysis of the interactions driving small molecule–RNA recognition - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC7549050/#:~:text=aliphatic%20and%20aromatic%20carbons%20within,when%20ligand%20positively%20charged%20atoms))

12.  ([Characterizing RNA-binding ligands on structures, chemical information, binding affinity and drug-likeness - PubMed](https://pubmed.ncbi.nlm.nih.gov/37415294/#:~:text=comprehensively%2C%20especially%20in%20the%20binding,ligand))

13.  ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=rely%20on%20scoring%20the%20possible,the%20predicted%20scores%20and%20the)) ([
            RNA-ligand molecular docking: advances and challenges - PMC
        ](https://pmc.ncbi.nlm.nih.gov/articles/PMC10250017/#:~:text=match%20at%20L665%20For%20example%2C,The%20accuracy%20may%20be%20further))

14.  ([Principles for targeting RNA with drug-like small molecules - PubMed](https://pubmed.ncbi.nlm.nih.gov/29977051/#:~:text=Structures%20are%20coloured%20by%20pocket,Pocket%20for%20linezolid%20in%20the))
