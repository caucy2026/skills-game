# 固定分析员输出故障排查

真实案例：模型正常完成，返回有效JSON，但plan不是字符串；严格运行时拒绝结果，自动模型门禁耗尽后不再竞标。不要将此归为欠费、房间断联或模型未启动。先读取当前任务合同、工具回执和固定分析会话的最后终态。

维护元数据scope=ownedDebugOnly为空不能证明不存在内部分析员或全部业务历史。session_status的NOT_ACTIVE也不证明会话被删除；核对原session投影及正式删除账本，只读，不清锁。固定session由房间、设备与workspace生成；正式删除后遵守账本生成继任，不复活原ID。

Node一次性zstd解码只读首帧。复用项目vibekits-session-rebind.mjs的完整帧遍历，逐次使用engine.bytesWritten推进，设置输入与总解压上限；只输出最后turn/end的kind、状态码和分析结果字段类型，不保存正文。文件存在、completed或JSON.parse成功均不能替代完整决策Schema。

提示明确canDo:boolean、plan:string（≤4000字符）、estimatedSeconds:1–86400整数、capabilityClaims/evidenceRefs:string[]（每项≤512字符）。多个步骤写同一字符串。保持严格校验，不把数组自动拼接当作已合格，不能通过改requiresModel/requiresAgent绕过任务合同。

回归：无效plan数组须拒绝，修正输入继续同一固定会话；过期合同零模型请求，权限/唯一租约不变。源码通过之后需最终签名包真实模型与竞标复验。不得热改签名或运行包，不因失败自动创建会话/无限重试；恢复前先对账当前模型选择、凭据健康与原失败终态。
