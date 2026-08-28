# 量潮沟通云产品规格书

## 故事

这是一座精密的水系枢纽，而我，是这里的守闸人。
每天，无数名为“碎片”的水滴从四面八方涌来。有的来自清晨拥挤的地铁，带着语音的温热；有的来自深夜的设计稿，沾着像素的冷光。它们七零八落，甚至带着错别字和前言不搭后语。
我不嫌弃它们。我的第一道闸门叫“雨水池”。
我张开网，接住这些急促的雨滴。我不催促它们成型，也不要求它们排队。我只默默地给每一滴水贴上隐形的标签：时间、地点、来路。然后，我让它们静静地沉淀。
当水池里的水积攒到一定程度，我就开始干活了。我不休息，像个患了强迫症的图书管理员，在夜深人静或者没人盯着我的时候，一头扎进水池里。
我用手去摸这些水的温度，用鼻子去闻它们的味道。
“嗯，这几滴都在念叨‘支付超时’，是一伙的。”
我把它们轻轻捞起，倒进一个贴着“支付流程优化”标签的透明玻璃缸里。
我绝不擅自把水过滤成纯净水，也不往里面加糖。我只是把同味的水归拢，然后给这个缸插上一面小旗：备忘草稿#1。
这就是我的第二道闸门：备忘区。
到了白天，人们围在玻璃缸前。这时候，我是隐身的。我看着他们指着水面说：“这滴好，这滴留着。”我也看着他们吵吵嚷嚷，水花四溅。
我只做一件事：盯着谁说了话。
当有人伸手，把那句“方案一，风险可控”从缸里捞出来，郑重地放进旁边那个金色的、闪闪发光的小盒子——共识区时，我立刻动了起来。
“咔哒。”
我在盒底狠狠盖下一个戳：2026-08-28 14:23。参与人：老张、小林、阿美。
那个瞬间，这滴水不再是随时会蒸发的闲聊，它被冻结成了坚固的冰晶。它有了重量，有了承诺。
一旦冰晶落成，我的第三道闸门就感应到了。
我是这套系统的老笔杆子。我把那块冰晶小心翼翼地捧出来，放在桌上。我不添油加醋，但我会给它穿上西装。我根据冰晶的成色，自动从通讯录里拉来相关的名单，把测试和运维的名字填好。然后，我把冰晶融化成一段体面、正式的文字，放进信封，封好口。
我把它搁在出风口，只等小林来，扫一眼，点一下“发送”。
它就化作一封邮件，飞向了正式的流转世界。
两周后，人们回头看。
我给他们展示这条河的走向。每一滴水从哪里落下，在哪口缸里打了个转，什么时候冻成了冰，什么时候寄了出去——我都记得清清楚楚，一笔不落。
我不聪明。我不会替他们决定该选哪滴水，也不会教他们怎么吵架。
我只是一个记性特别好的守闸人。我替他们把水归好类，把承诺冻成冰，把冰打扮成邮件发出去。
真正聪明的，是那些终于从暴雨里钻出来，站在我的闸门前，安安静静指着一缸水说“就它了”的人。

## 规格

将领域命名从 Shard（碎片）改为 Message（消息），是一个非常务实且具备工程前瞻性的决定。这不仅让领域模型更通用，也降低了系统与外部（如微信、钉钉、邮件）对接时的心智负担。
以下是调整后的完整事件风暴文档，核心聚合、事件载荷和状态机均已同步更新，并在 Message 上增加了 externalRef 等兼容性字段。
事件风暴：沟通云系统（Message 兼容版）
一、 事件溯源全景图
📱 随时随地                    🤖 后台异步                   👥 同步协作                   ✉️ 异步通知                  📚 静默留存
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  雨水池  ──────→  AI搬运工  ──────→  备忘容器  ──────→  共识固化  ──────→  邮件草稿  ──────→  归档时间轴
  (消息涌入)      (聚类搬运)        (讨论·收敛)      (状态冻结)      (格式化输出)      (全链路追溯)
──────┬───────────────────┬──────────────────┬──────────────────┬──────────────────┬──────────────────
  E1  │ E2  E4           E5│E6  E7           E8│E9              E10│E11             E12│E13
二、 命令
编号	命令名	发起者	触发时机	前置条件	
C1	SubmitMessage	用户/外部系统	唤起输入窗口或接收转发消息	无门槛，零成本	
C2	StartClusterScan	系统(定时器)	每次扫描周期到达	雨水池有未归类消息	
C6	OpenMemo	用户	点击备忘卡片	备忘已生成	
C7	PostDiscussion	用户	在讨论区发消息	备忘处于开放状态	
C9	MarkConsensus	用户	点击”标记共识“按钮	至少有一条讨论消息	
C10	GenerateEmailDraft	系统	共识状态变更触发	备忘状态=已共识	
C11	SendEmail	用户	审阅草稿后点击”发送“	邮件草稿已生成	
C13	ViewTimeline	用户	拖动看板到归档区	至少有一个已归档项	
三、 领域事件
E1 — MessageSubmitted（消息已投递）
发生时机：用户完成语音/文字输入，或外部系统转发消息进入
事件载荷：
{
  messageId: ”msg-20260824091701“,
  rawContent: ”支付失败的时候，用户应该能一键重试...“,
  inputType: ”VOICE“,          // VOICE | TEXT | FORWARD | API_PUSH
  timestamp: ”2026-08-24T09:17+08:00“,
  sourceContext: {
    device: ”iPhone“,
    location: ”地铁“,
    originApp: ”WeChat“        // 兼容外部来源
  },
  externalRef: {               // 【兼容性设计】对接外部系统的唯一标识
    externalId: ”wx-msg-123456“,
    externalSystem: ”WECHAT“
  },
  status: ”RAIN_DROP“          // 落入雨水池
}
所属聚合：MessageAggregate（消息聚合）
状态迁移：null → RAIN_DROP
下游影响：触发 E2
E2 — ClusterScanStarted（扫描已启动）
发生时机：定时器到达 / 雨水池消息数超过阈值
事件载荷：
{
  scanId: ”scan-20260824020000“,
  triggerType: ”SCHEDULED“,     
  pendingMessageCount: 7,
  startedAt: ”2026-08-24T02:00:00+08:00“
}
所属聚合：ClusterScanAggregate
状态迁移：IDLE → SCANNING
下游影响：触发 E3 (内部聚类处理)
E4 — MessageRelocated（消息已搬运）
发生时机：AI将消息从雨水池移入备忘草稿
事件载荷：
{
  messageId: ”msg-20260824091701“,
  fromPool: ”RAIN_DROP“,
  toMemoId: ”memo-003“,
  relocatedBy: ”AI“,
  relocatedAt: ”2026-08-24T02:03:00+08:00“
}
所属聚合：MessageAggregate
状态迁移：RAIN_DROP → IN_MEMO
不变量：同一消息不可同时存在于多个备忘中
E5 — MemoDrafted（备忘草稿已生成）
发生时机：AI完成语义聚类，将消息归入容器
事件载荷：
{
  memoId: ”memo-003“,
  title: ”一键重试功能“,
  label: ”待整理“,
  labelColor: ”LIGHT_GRAY“,
  relatedMessages: [”msg-20260824091701“],
  containerFolder: ”支付流程优化“,
  generatedBy: ”AI“,
  generatedAt: ”2026-08-24T02:03:00+08:00“,
  discussionZone: [”msg-20260824091701“],  // 原始消息自动进入讨论区
  consensusZone: null                       
}
所属聚合：MemoAggregate（备忘聚合）
状态迁移：null → DRAFT
不变量：原始消息内容不可被AI修改，原样保留
下游影响：等待 E6
E6 — DiscussionPosted（讨论消息已发送）
发生时机：用户在讨论区发表意见（复用 MessageSubmitted，但状态直接进入 IN_MEMO）
事件载荷：
{
  memoId: ”memo-001“,
  messageId: ”msg-20260828142100“,
  messageContent: ”方案一，改动小，风险可控。“,
  postedBy: ”user-laozhang“,
  postedAt: ”2026-08-28T14:21:00+08:00“,
  zone: ”DISCUSSION“           
}
所属聚合：MemoAggregate → DiscussionZone
不变量：讨论区消息不得被删除，只可追加
E8 — ConsensusMarked（共识已标记）
发生时机：用户点击”标记共识“
事件载荷：
{
  memoId: ”memo-001“,
  consensusContent: ”方案一，改动小，风险可控。“,
  sourceMessageId: ”msg-20260828142100“,  // 溯源到具体某条讨论消息
  markedBy: ”user-xiaolin“,
  markedAt: ”2026-08-28T14:23:00+08:00“,
  participants: [”user-laozhang“, ”user-xiaolin“, ”user-amei“],
  timestampImprinted: true     
}
所属聚合：MemoAggregate → ConsensusZone
状态迁移：OPEN → CONSENSUS_REACHED
不变量：
  ① 共识区只能由”标记共识“动作写入
  ② 一旦写入，只追加不删除（形成不可篡改决策链）
  ③ 时间戳与参与人列表由系统自动注入
下游影响：触发 E10
E10 — EmailDraftGenerated（邮件草稿已生成）
发生时机：备忘状态变更为 CONSENSUS_REACHED
事件载荷：
{
  emailDraftId: ”draft-20260829090001“,
  sourceMemoId: ”memo-001“,
  sourceConsensusId: ”consensus-001“,
  subject: ”关于支付超时处理方案的技术通知“,
  body: ”根据团队讨论共识，支付超时处理采用方案一...“,
  memoLink: ”https://沟通云/memo/001“,
  recipients: [”user-laozhang“, ”user-xiaolin“, ”user-amei“],
  cc: [”qa-team“, ”ops-team“],       
  generatedBy: ”AI“,
  generatedAt: ”2026-08-29T09:00:00+08:00“,
  status: ”DRAFT_PENDING_REVIEW“
}
所属聚合：EmailAggregate（邮件聚合）
状态迁移：null → DRAFT_PENDING_REVIEW
不变量：
  ① 邮件正文必须包含来源备忘的链接（血缘锚点）
  ② 收件人列表必须包含共识参与人
  ③ 草稿不得自动发送，必须人工审核
E11 — EmailSent（邮件已发送）
发生时机：用户审阅草稿后点击发送
事件载荷：
{
  emailDraftId: ”draft-20260829090001“,
  sentBy: ”user-xiaolin“,
  sentAt: ”2026-08-29T09:01:00+08:00“,
  externalMessageId: ”smtp-xxxxxxx“   // 邮件发送系统的 messageId
}
所属聚合：EmailAggregate
状态迁移：DRAFT_PENDING_REVIEW → SENT
下游影响：触发 E12
E12 — MemoArchived（备忘已归档）
发生时机：关联邮件发送完成 / 迭代周期结束手动归档
事件载荷：
{
  memoId: ”memo-001“,
  archivedAt: ”2026-09-11T17:30:00+08:00“,
  archiveReason: ”ITERATION_END“,
  lineageChain: [
    { stage: ”MESSAGE“,    ref: ”msg-20260824091701“,  at: ”2026-08-24T09:17“ },
    { stage: ”MEMO“,       ref: ”memo-001“,               at: ”2026-08-24T02:03“ },
    { stage: ”CONSENSUS“,  ref: ”consensus-001“,          at: ”2026-08-28T14:23“ },
    { stage: ”EMAIL“,      ref: ”draft-20260829090001“,   at: ”2026-08-29T09:01“ }
  ],
  fullTraceRetrievable: true    
}
所属聚合：MemoAggregate
状态迁移：CONSENSUS_REACHED → ARCHIVED
不变量：归档后只读，整条血缘链不可修改
四、 聚合根与边界
┌──────────────────────────────────────────────────────────────────────┐
│                    Bounded Context: 沟通云                             │
│                                                                       │
│  ┌────────────────┐  ┌──────────────────────┐  ┌──────────────────┐  │
│  │MessageAggregate │  │  MemoAggregate        │  │ EmailAggregate   │  │
│  │  (消息聚合)      │  │  (备忘聚合)           │  │ (邮件聚合)        │  │
│  │                │  │                      │  │                  │  │
│  │ - messageId    │  │ - memoId             │  │ - emailDraftId   │  │
│  │ - rawContent   │  │ - discussionZone     │  │ - sourceMemoId   │  │
│  │ - externalRef  │  │ - consensusZone      │  │ - body           │  │
│  │ - inputType    │  │ - status             │  │ - recipients     │  │
│  │ - status       │  │ - participants       │  │ - status         │  │
│  │                │  │ - lineageChain       │  │                  │  │
│  │ (不变量:         │  │ (不变量:              │  │ (不变量:          │  │
│  │ 原文不可篡改;     │  │  共识只追加不删除)     │  │  草稿须人工发送)   │  │
│  │ 带外部引用ID)    │  │                      │  │                  │  │
│  └───────┬────────┘  └──────────┬───────────┘  └────────┬─────────┘  │
│          └────────── E4 ────────┘                      │            │
│                                 └────────── E10 ───────┘            │
│  ┌──────────────────┐                                          │
│  │ ClusterScanAgg    │   (系统内部聚合，不面向用户)                  │
│  │ (聚类扫描聚合)     │                                          │
│  └──────────────────┘                                          │
└──────────────────────────────────────────────────────────────────────┘
五、 状态机
备忘生命周期（核心状态机）
       E5                E6               E9              E12
 null ──→ DRAFT ──→ OPEN ──→ CONSENSUS_REACHED ──→ ARCHIVED
          │                                        ↑
          │ E4: 消息搬运进来                         │ E12: 邮件发送/迭代结束
          ↓                                        │
        (AI生成，用户不参与)                          │ (只读，锁定)
                                                  │
                            E8: 如果未标记共识 ──→ 停留在 OPEN
                                                  │    (不会触发邮件，不会归档)
消息生命周期
 E1              E4
null ──→ RAIN_DROP ──→ IN_MEMO ──→ (随备忘归档)
         (雨水池)      (进入讨论区)
兼容场景：外部消息通过 API_PUSH 直接进入 RAIN_DROP
邮件生命周期
 E10                      E11
null ──→ DRAFT_PENDING_REVIEW ──→ SENT
         (AI生成，等待人工审核)     (已发出，不可撤回)
六、 Read Model（读模型/投影）
┌─────────────────────────────────────────────────────────────────┐
│                    Read Model: 看板视图                         │
│                                                                │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐│
│  │  雨水    │  │  备忘    │  │  共识    │  │  邮件    │  │  归档    ││
│  │         │  │         │  │         │  │         │  │         ││
│  │ msg-1   │  │ memo-1  │  │ memo-1  │  │draft-1  │  │ memo-1  ││
│  │ msg-2   │  │  待整理  │  │  已共识  │  │  已发送  │  │ lineage ││
│  │ msg-3   │  │ memo-2  │  │         │  │         │  │  chain  ││
│  │         │  │  待讨论  │  │         │  │         │  │         ││
│  │         │  │         │  │ (带时间戳│  │ (带备忘  │  │ (完整    ││
│  │ (按时间  │  │         │  │ 和参与人)│  │  链接)   │  │  链路)  ││
│  │  倒序)   │  │         │  │         │  │         │  │         ││
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘│
│                                                                │
│  读模型从事件流实时投影：                                          │
│  E1 → 更新雨水列                                                 │
│  E5,E4 → 更新备忘列                                               │
│  E9 → 卡片从备忘列移到共识列                                       │
│  E10,E11 → 更新邮件列                                            │
│  E12 → 卡片移到归档列                                             │
└─────────────────────────────────────────────────────────────────┘
七、 Policy（策略/反应规则）
┌──────────────────────────────┬─────────────────────────────────────────────┐
│ When（当...事件发生）            │ Then（自动触发...命令）                          │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E1: MessageSubmitted         │ → C2: 检查是否到达扫描阈值                     │
│                              │   (未到阈值则等待定时器触发)                     │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E2: ClusterScanStarted       │ → C3(内部): 执行语义聚类                       │
│                              │ → C4(内部): 将消息搬运至对应备忘                 │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E9: ConsensusMarked          │ → C10: GenerateEmailDraft                    │
│ (核心Policy)                   │   (共识达成是邮件生成的唯一触发条件)               │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E11: EmailSent               │ → C12(内部): 将备忘状态推至 ARCHIVED          │
│                              │ → C13(内部): 组装 lineageChain 写入归档        │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E8: (备忘未标记共识)             │ → 不触发任何动作                                │
│                              │   (保持 OPEN，静默等待)                         │
└──────────────────────────────┴─────────────────────────────────────────────┘
八、 外部系统与限界上下文交互
                                    ┌──────────────────┐
                                    │  语音转文字服务     │
                                    │ (External System) │
                                    └────────┬─────────┘
                                             │ E1 时调用
                                             ▼
┌─────────────┐  E1   ┌──────────────────────────────────┐  E11  ┌──────────────┐
│  用户终端     │──────→│                                    │──────→│  SMTP邮件服务  │
│ (手机/PC)   │       │        沟通云 限界上下文               │       │ (External)   │
└─────────────┘       │                                    └──────┘└──────────────┘
       ↑              │                                    │  E10
       │              │  ┌──────────┐  ┌──────────────┐  │──────→ 自动补全收件人
       │              │  │ AI聚类引擎│  │ 组织架构服务    │  │
       │              │  │ (GLM)   │  │ (External)    │  │
       │              │  └──────────┘  └──────────────┘  │
       │              │  ┌──────────────────┐            │
       │              │  │ 事件存储          │            │
       │              │  │ (Event Store)    │            │
       │              │  │  全量事件流不可变   │            │
       │              │  └──────────────────┘            │
       │              └──────────────────────────────────┘
       │ E1 (API_PUSH)
       │
┌──────┴───────────┐
│ 外部消息源        │  ← 兼容微信/钉钉/飞书等第三方转发或API推送
│ (WeChat/DingTalk)│
└──────────────────┘
九、 核心不变量清单
编号	不变量	约束范围	含义	
INV-1	原文不可篡改	MessageAggregate	消息一旦提交，rawContent 永不修改，AI 只搬运不修改	
INV-2	外部引用可追溯	MessageAggregate	外部系统转入的消息必须保留 externalRef，确保可回溯到原始平台	
INV-3	讨论只追加	DiscussionZone	讨论区消息不可删除、不可编辑，只可追加	
INV-4	共识需手动标记	ConsensusZone	共识区只能由 MarkConsensus 命令写入，AI 不得自动生成共识	
INV-5	时间戳不可篡改	ConsensusZone	共识一旦标记，时间戳和参与人列表冻结，永不修改	
INV-6	邮件需人工发送	EmailAggregate	草稿只能由人执行 SendEmail，系统不得自动发送	
INV-7	邮件必须带血缘链接	EmailAggregate	邮件正文必须包含来源备忘的链接，确保可回溯	
INV-8	归档后只读	MemoAggregate (ARCHIVED)	归档后所有字段只读，lineageChain 锁定	
INV-9	全链路可追溯	全局	从消息到归档，任意阶段可逆向追溯至前一阶段	
十、 一句话总结
事件风暴视角下，沟通云的本质是一个消息流转的状态机：
消息涌入 → AI 异步搬运 → 人在容器内讨论 → 人工标记共识触发状态冻结 → AI 生成邮件草稿 → 人工审核发送 → 归档锁定全链路。
AI 负责搬运与格式化，人负责选择与拍板，事件负责记住一切。
将领域命名从 Shard（碎片）改为 Message（消息），是一个非常务实且具备工程前瞻性的决定。这不仅让领域模型更通用，也降低了系统与外部（如微信、钉钉、邮件）对接时的心智负担。
以下是调整后的完整事件风暴文档，核心聚合、事件载荷和状态机均已同步更新，并在 Message 上增加了 externalRef 等兼容性字段。
事件风暴：沟通云系统（Message 兼容版）
一、 事件溯源全景图
📱 随时随地                    🤖 后台异步                   👥 同步协作                   ✉️ 异步通知                  📚 静默留存
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  雨水池  ──────→  AI搬运工  ──────→  备忘容器  ──────→  共识固化  ──────→  邮件草稿  ──────→  归档时间轴
  (消息涌入)      (聚类搬运)        (讨论·收敛)      (状态冻结)      (格式化输出)      (全链路追溯)
──────┬───────────────────┬──────────────────┬──────────────────┬──────────────────┬──────────────────
  E1  │ E2  E4           E5│E6  E7           E8│E9              E10│E11             E12│E13
二、 命令
编号	命令名	发起者	触发时机	前置条件	
C1	SubmitMessage	用户/外部系统	唤起输入窗口或接收转发消息	无门槛，零成本	
C2	StartClusterScan	系统(定时器)	每次扫描周期到达	雨水池有未归类消息	
C6	OpenMemo	用户	点击备忘卡片	备忘已生成	
C7	PostDiscussion	用户	在讨论区发消息	备忘处于开放状态	
C9	MarkConsensus	用户	点击”标记共识“按钮	至少有一条讨论消息	
C10	GenerateEmailDraft	系统	共识状态变更触发	备忘状态=已共识	
C11	SendEmail	用户	审阅草稿后点击”发送“	邮件草稿已生成	
C13	ViewTimeline	用户	拖动看板到归档区	至少有一个已归档项	
三、 领域事件
E1 — MessageSubmitted（消息已投递）
发生时机：用户完成语音/文字输入，或外部系统转发消息进入
事件载荷：
{
  messageId: ”msg-20260824091701“,
  rawContent: ”支付失败的时候，用户应该能一键重试...“,
  inputType: ”VOICE“,          // VOICE | TEXT | FORWARD | API_PUSH
  timestamp: ”2026-08-24T09:17+08:00“,
  sourceContext: {
    device: ”iPhone“,
    location: ”地铁“,
    originApp: ”WeChat“        // 兼容外部来源
  },
  externalRef: {               // 【兼容性设计】对接外部系统的唯一标识
    externalId: ”wx-msg-123456“,
    externalSystem: ”WECHAT“
  },
  status: ”RAIN_DROP“          // 落入雨水池
}
所属聚合：MessageAggregate（消息聚合）
状态迁移：null → RAIN_DROP
下游影响：触发 E2
E2 — ClusterScanStarted（扫描已启动）
发生时机：定时器到达 / 雨水池消息数超过阈值
事件载荷：
{
  scanId: ”scan-20260824020000“,
  triggerType: ”SCHEDULED“,     
  pendingMessageCount: 7,
  startedAt: ”2026-08-24T02:00:00+08:00“
}
所属聚合：ClusterScanAggregate
状态迁移：IDLE → SCANNING
下游影响：触发 E3 (内部聚类处理)
E4 — MessageRelocated（消息已搬运）
发生时机：AI将消息从雨水池移入备忘草稿
事件载荷：
{
  messageId: ”msg-20260824091701“,
  fromPool: ”RAIN_DROP“,
  toMemoId: ”memo-003“,
  relocatedBy: ”AI“,
  relocatedAt: ”2026-08-24T02:03:00+08:00“
}
所属聚合：MessageAggregate
状态迁移：RAIN_DROP → IN_MEMO
不变量：同一消息不可同时存在于多个备忘中
E5 — MemoDrafted（备忘草稿已生成）
发生时机：AI完成语义聚类，将消息归入容器
事件载荷：
{
  memoId: ”memo-003“,
  title: ”一键重试功能“,
  label: ”待整理“,
  labelColor: ”LIGHT_GRAY“,
  relatedMessages: [”msg-20260824091701“],
  containerFolder: ”支付流程优化“,
  generatedBy: ”AI“,
  generatedAt: ”2026-08-24T02:03:00+08:00“,
  discussionZone: [”msg-20260824091701“],  // 原始消息自动进入讨论区
  consensusZone: null                       
}
所属聚合：MemoAggregate（备忘聚合）
状态迁移：null → DRAFT
不变量：原始消息内容不可被AI修改，原样保留
下游影响：等待 E6
E6 — DiscussionPosted（讨论消息已发送）
发生时机：用户在讨论区发表意见（复用 MessageSubmitted，但状态直接进入 IN_MEMO）
事件载荷：
{
  memoId: ”memo-001“,
  messageId: ”msg-20260828142100“,
  messageContent: ”方案一，改动小，风险可控。“,
  postedBy: ”user-laozhang“,
  postedAt: ”2026-08-28T14:21:00+08:00“,
  zone: ”DISCUSSION“           
}
所属聚合：MemoAggregate → DiscussionZone
不变量：讨论区消息不得被删除，只可追加
E8 — ConsensusMarked（共识已标记）
发生时机：用户点击”标记共识“
事件载荷：
{
  memoId: ”memo-001“,
  consensusContent: ”方案一，改动小，风险可控。“,
  sourceMessageId: ”msg-20260828142100“,  // 溯源到具体某条讨论消息
  markedBy: ”user-xiaolin“,
  markedAt: ”2026-08-28T14:23:00+08:00“,
  participants: [”user-laozhang“, ”user-xiaolin“, ”user-amei“],
  timestampImprinted: true     
}
所属聚合：MemoAggregate → ConsensusZone
状态迁移：OPEN → CONSENSUS_REACHED
不变量：
  ① 共识区只能由”标记共识“动作写入
  ② 一旦写入，只追加不删除（形成不可篡改决策链）
  ③ 时间戳与参与人列表由系统自动注入
下游影响：触发 E10
E10 — EmailDraftGenerated（邮件草稿已生成）
发生时机：备忘状态变更为 CONSENSUS_REACHED
事件载荷：
{
  emailDraftId: ”draft-20260829090001“,
  sourceMemoId: ”memo-001“,
  sourceConsensusId: ”consensus-001“,
  subject: ”关于支付超时处理方案的技术通知“,
  body: ”根据团队讨论共识，支付超时处理采用方案一...“,
  memoLink: ”https://沟通云/memo/001“,
  recipients: [”user-laozhang“, ”user-xiaolin“, ”user-amei“],
  cc: [”qa-team“, ”ops-team“],       
  generatedBy: ”AI“,
  generatedAt: ”2026-08-29T09:00:00+08:00“,
  status: ”DRAFT_PENDING_REVIEW“
}
所属聚合：EmailAggregate（邮件聚合）
状态迁移：null → DRAFT_PENDING_REVIEW
不变量：
  ① 邮件正文必须包含来源备忘的链接（血缘锚点）
  ② 收件人列表必须包含共识参与人
  ③ 草稿不得自动发送，必须人工审核
E11 — EmailSent（邮件已发送）
发生时机：用户审阅草稿后点击发送
事件载荷：
{
  emailDraftId: ”draft-20260829090001“,
  sentBy: ”user-xiaolin“,
  sentAt: ”2026-08-29T09:01:00+08:00“,
  externalMessageId: ”smtp-xxxxxxx“   // 邮件发送系统的 messageId
}
所属聚合：EmailAggregate
状态迁移：DRAFT_PENDING_REVIEW → SENT
下游影响：触发 E12
E12 — MemoArchived（备忘已归档）
发生时机：关联邮件发送完成 / 迭代周期结束手动归档
事件载荷：
{
  memoId: ”memo-001“,
  archivedAt: ”2026-09-11T17:30:00+08:00“,
  archiveReason: ”ITERATION_END“,
  lineageChain: [
    { stage: ”MESSAGE“,    ref: ”msg-20260824091701“,  at: ”2026-08-24T09:17“ },
    { stage: ”MEMO“,       ref: ”memo-001“,               at: ”2026-08-24T02:03“ },
    { stage: ”CONSENSUS“,  ref: ”consensus-001“,          at: ”2026-08-28T14:23“ },
    { stage: ”EMAIL“,      ref: ”draft-20260829090001“,   at: ”2026-08-29T09:01“ }
  ],
  fullTraceRetrievable: true    
}
所属聚合：MemoAggregate
状态迁移：CONSENSUS_REACHED → ARCHIVED
不变量：归档后只读，整条血缘链不可修改
四、 聚合根与边界
┌──────────────────────────────────────────────────────────────────────┐
│                    Bounded Context: 沟通云                             │
│                                                                       │
│  ┌────────────────┐  ┌──────────────────────┐  ┌──────────────────┐  │
│  │MessageAggregate │  │  MemoAggregate        │  │ EmailAggregate   │  │
│  │  (消息聚合)      │  │  (备忘聚合)           │  │ (邮件聚合)        │  │
│  │                │  │                      │  │                  │  │
│  │ - messageId    │  │ - memoId             │  │ - emailDraftId   │  │
│  │ - rawContent   │  │ - discussionZone     │  │ - sourceMemoId   │  │
│  │ - externalRef  │  │ - consensusZone      │  │ - body           │  │
│  │ - inputType    │  │ - status             │  │ - recipients     │  │
│  │ - status       │  │ - participants       │  │ - status         │  │
│  │                │  │ - lineageChain       │  │                  │  │
│  │ (不变量:         │  │ (不变量:              │  │ (不变量:          │  │
│  │ 原文不可篡改;     │  │  共识只追加不删除)     │  │  草稿须人工发送)   │  │
│  │ 带外部引用ID)    │  │                      │  │                  │  │
│  └───────┬────────┘  └──────────┬───────────┘  └────────┬─────────┘  │
│          └────────── E4 ────────┘                      │            │
│                                 └────────── E10 ───────┘            │
│  ┌──────────────────┐                                          │
│  │ ClusterScanAgg    │   (系统内部聚合，不面向用户)                  │
│  │ (聚类扫描聚合)     │                                          │
│  └──────────────────┘                                          │
└──────────────────────────────────────────────────────────────────────┘
五、 状态机
备忘生命周期（核心状态机）
       E5                E6               E9              E12
 null ──→ DRAFT ──→ OPEN ──→ CONSENSUS_REACHED ──→ ARCHIVED
          │                                        ↑
          │ E4: 消息搬运进来                         │ E12: 邮件发送/迭代结束
          ↓                                        │
        (AI生成，用户不参与)                          │ (只读，锁定)
                                                  │
                            E8: 如果未标记共识 ──→ 停留在 OPEN
                                                  │    (不会触发邮件，不会归档)
消息生命周期
 E1              E4
null ──→ RAIN_DROP ──→ IN_MEMO ──→ (随备忘归档)
         (雨水池)      (进入讨论区)
兼容场景：外部消息通过 API_PUSH 直接进入 RAIN_DROP
邮件生命周期
 E10                      E11
null ──→ DRAFT_PENDING_REVIEW ──→ SENT
         (AI生成，等待人工审核)     (已发出，不可撤回)
六、 Read Model（读模型/投影）
┌─────────────────────────────────────────────────────────────────┐
│                    Read Model: 看板视图                         │
│                                                                │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐│
│  │  雨水    │  │  备忘    │  │  共识    │  │  邮件    │  │  归档    ││
│  │         │  │         │  │         │  │         │  │         ││
│  │ msg-1   │  │ memo-1  │  │ memo-1  │  │draft-1  │  │ memo-1  ││
│  │ msg-2   │  │  待整理  │  │  已共识  │  │  已发送  │  │ lineage ││
│  │ msg-3   │  │ memo-2  │  │         │  │         │  │  chain  ││
│  │         │  │  待讨论  │  │         │  │         │  │         ││
│  │         │  │         │  │ (带时间戳│  │ (带备忘  │  │ (完整    ││
│  │ (按时间  │  │         │  │ 和参与人)│  │  链接)   │  │  链路)  ││
│  │  倒序)   │  │         │  │         │  │         │  │         ││
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘│
│                                                                │
│  读模型从事件流实时投影：                                          │
│  E1 → 更新雨水列                                                 │
│  E5,E4 → 更新备忘列                                               │
│  E9 → 卡片从备忘列移到共识列                                       │
│  E10,E11 → 更新邮件列                                            │
│  E12 → 卡片移到归档列                                             │
└─────────────────────────────────────────────────────────────────┘
七、 Policy（策略/反应规则）
┌──────────────────────────────┬─────────────────────────────────────────────┐
│ When（当...事件发生）            │ Then（自动触发...命令）                          │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E1: MessageSubmitted         │ → C2: 检查是否到达扫描阈值                     │
│                              │   (未到阈值则等待定时器触发)                     │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E2: ClusterScanStarted       │ → C3(内部): 执行语义聚类                       │
│                              │ → C4(内部): 将消息搬运至对应备忘                 │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E9: ConsensusMarked          │ → C10: GenerateEmailDraft                    │
│ (核心Policy)                   │   (共识达成是邮件生成的唯一触发条件)               │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E11: EmailSent               │ → C12(内部): 将备忘状态推至 ARCHIVED          │
│                              │ → C13(内部): 组装 lineageChain 写入归档        │
├──────────────────────────────┼─────────────────────────────────────────────┤
│ E8: (备忘未标记共识)             │ → 不触发任何动作                                │
│                              │   (保持 OPEN，静默等待)                         │
└──────────────────────────────┴─────────────────────────────────────────────┘
八、 外部系统与限界上下文交互
                                    ┌──────────────────┐
                                    │  语音转文字服务     │
                                    │ (External System) │
                                    └────────┬─────────┘
                                             │ E1 时调用
                                             ▼
┌─────────────┐  E1   ┌──────────────────────────────────┐  E11  ┌──────────────┐
│  用户终端     │──────→│                                    │──────→│  SMTP邮件服务  │
│ (手机/PC)   │       │        沟通云 限界上下文               │       │ (External)   │
└─────────────┘       │                                    └──────┘└──────────────┘
       ↑              │                                    │  E10
       │              │  ┌──────────┐  ┌──────────────┐  │──────→ 自动补全收件人
       │              │  │ AI聚类引擎│  │ 组织架构服务    │  │
       │              │  │ (GLM)   │  │ (External)    │  │
       │              │  └──────────┘  └──────────────┘  │
       │              │  ┌──────────────────┐            │
       │              │  │ 事件存储          │            │
       │              │  │ (Event Store)    │            │
       │              │  │  全量事件流不可变   │            │
       │              │  └──────────────────┘            │
       │              └──────────────────────────────────┘
       │ E1 (API_PUSH)
       │
┌──────┴───────────┐
│ 外部消息源        │  ← 兼容微信/钉钉/飞书等第三方转发或API推送
│ (WeChat/DingTalk)│
└──────────────────┘
九、 核心不变量清单
编号	不变量	约束范围	含义	
INV-1	原文不可篡改	MessageAggregate	消息一旦提交，rawContent 永不修改，AI 只搬运不修改	
INV-2	外部引用可追溯	MessageAggregate	外部系统转入的消息必须保留 externalRef，确保可回溯到原始平台	
INV-3	讨论只追加	DiscussionZone	讨论区消息不可删除、不可编辑，只可追加	
INV-4	共识需手动标记	ConsensusZone	共识区只能由 MarkConsensus 命令写入，AI 不得自动生成共识	
INV-5	时间戳不可篡改	ConsensusZone	共识一旦标记，时间戳和参与人列表冻结，永不修改	
INV-6	邮件需人工发送	EmailAggregate	草稿只能由人执行 SendEmail，系统不得自动发送	
INV-7	邮件必须带血缘链接	EmailAggregate	邮件正文必须包含来源备忘的链接，确保可回溯	
INV-8	归档后只读	MemoAggregate (ARCHIVED)	归档后所有字段只读，lineageChain 锁定	
INV-9	全链路可追溯	全局	从消息到归档，任意阶段可逆向追溯至前一阶段	
十、 一句话总结
事件风暴视角下，沟通云的本质是一个消息流转的状态机：
消息涌入 → AI 异步搬运 → 人在容器内讨论 → 人工标记共识触发状态冻结 → AI 生成邮件草稿 → 人工审核发送 → 归档锁定全链路。
AI 负责搬运与格式化，人负责选择与拍板，事件负责记住一切。