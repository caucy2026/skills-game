# XP恢复耗时复盘与固定流程（2026-10-04）

本轮未完成三分钟登录及前后台业务闭环。r390从首个窗口排查文件创建起就超过314秒，该起点晚于实际开始，因此只是耗时下界。总耗时不能从中扣掉工具往返、思考或更换编号后重新计时。没有成功登录时，不把方案称为“三分钟保证”。

## 已测时间及主要损耗

| 项目 | 实测 | 判定 |
|---|---:|---|
| 单次控制附加并进入账号页 | 15.17、15.44秒 | 两轮B局部实机成功，非完整恢复 |
| 账号页登录脚本 | 55.75、56.42、55.79秒 | 最终仍停选服，均不算登录成功 |
| 新普通键序列内部耗时 | 2.76秒；捕获后3.46秒 | A未打开登入器，不能算加速成功 |
| r390窗口排查及后续 | 至少314秒 | 180秒门失败 |

主要损耗由执行方式造成：把固定连续操作拆成许多工具和模型回合；窗口最小化时反复走只查可见窗口的入口；定向键未消费后没有立即采用已有备用输入；在恢复中反复找路径和写临时脚本；固定延迟后继续发选角，未等服务器阶段完成。不能把这些全部归为服务器慢。

A最后实测：未捕获时桌面无变化；按成熟普通点击捕获后，标题变为释放鼠标提示且“我的文档”被选中，但六次Down没有得到登入器，仍未打开。Home+六Down在B曾成功，不得仅因图标顺序相同就把A也记成功。保留差异，不继续无证据重复该键序列。

## 固定与保留动态判断的边界

用户最新分配：当前客户端任务固定第一台A，另一同事第二台B。A精确名称 `XP SWDOL Voodoo3`；B为 `XP SWDOL Voodoo3 B`。完整标题中的 ` - 86Box` 用于消除前缀歧义。PID、窗口号、游戏PID和后台代次每次动态发现，不能固定旧数字。任何输入peer必须用lsof核为本任务宿主PID，不暂停同事VM。

固定复用标准登入器→开始游戏→更新错误“是”、已有观察器、审核的单次控制附加、已验证输入1.5/1.5/0.4/2秒节奏和stdin凭据读取。窗口不可见时直接用成熟focus-86box-window取消最小化；不先新写窗口扫描。屏保只做已验证Shift唤醒。出现未消费输入立即走已有故障分流，不重新发明控制器。

普通键序列已收进 `tools/xp-original-control/normal_input_sequence.py`，带精确VM/PID/窗口、共享输入锁、前台检查、前后截图和继承全局截止；目前只是局部发送入口，A登入器启动仍未通过。它不自动选择后续页面或声称登录通过。注入15秒路线待A实证后再收为跨VM固定流程。

## 下一轮目标预算（未验收）

身份/当前页15秒；标准启动30秒；只补缺失观察与控制20秒；认证/选服/选角60秒；真实动作与同代后台事件35秒；余量20秒。合计180秒。准备在执行前完成，执行后只回读必要终态；后台独立只读核验与前台启动并行，输入/注入变更顺序执行。现有脚本选服阶段需要明确就绪门，不能把固定2秒当就绪或盲点选角。

连续复跑必须一次完整包括前后台就绪、真实进图和后台动作。超时保留失败，再读对应成功记录、核差异；不得通过缩小目标、改编号或减少必要身份门伪造达标。

## 2026-10-04: verified A recovery details (CPU comparison run)

A reached the original world with helen2 at Changyang (545,569). Credentials-to-world took 55.658s; DirectInput attachment took 15.419s. Full recovery exceeded the user's 240s limit. These component timings do not establish a cold-recovery PASS. Evidence: `/private/tmp/cpu300-helen2-login-20261004/report.json` and `world.png`; archive temporary evidence before treating it as a durable reference.

- `86box_trace_media.swift` argument 2 takes the exact VM NAME, e.g. `XP SWDOL Voodoo3`; it appends ` - 86Box` internally. In contrast, `with_86box_input.py --title` takes the discriminating title prefix. Do not interchange these parameters.
- Verify the mounted file with `lsof -a -p <currentPID> <ISO>`. Without `-a`, unrelated descriptors are printed.
- A desktop Run used the proven HID path after mouse capture. Game credentials used the default PID-targeted path. Verify the actual page and foreground; do not force one delivery mode on every input.
- Generate Windows path literals with proper quoting (`repr` when generating Python), not literal string substitution: backslash-a becomes a control character. Credentials remain stdin-only.
- An immediate screenshot after Run may still show the desktop. Inspect the result of the SAME operation before retrying. ARM later visibly reported `Base observer ready`; early absence was not failure.
- Resolve Desktop from Shell Folders, rather than assuming an English Desktop path. Existing LAUNCH2.BAT followed that route. Use the existing launcher click helper, and send Y only after seeing the update-error question.
- Verify every control peer against its actual host VM PID. This login rejected a different VM's connection before succeeding. Never assume the first peer is A.

CPU300 is a reversible diagnostic comparison, not a proven fix or a mandatory injection prerequisite. Do not change server timeouts or system time to conceal the failure.
