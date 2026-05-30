# DEBUG_STATE — 配网后灯节点偶发不响应 Vendor Model 命令
更新时间: 2026-05-30 14:20

Phase: 已排除 Mesh 接收层，正在确认 app 层是否真正消费了命令
Authoritative state: RTT 显示 vendor model opcode 0xC1 的 access callback 每次都进入并返回 0（来源：mesh 协议栈回调日志，权威）
活跃假设: app_control_mesh_set_effect() 收到命令但被当前控制权 owner 拦截，没下发到 animation
已排除:
  - 收发链路异常 — 协议栈回调 100% 进入，opcode/payload 正确
  - 配网丢失 — Composition Data 读回正常，节点仍在网
Rollback boundary: 最近可工作提交 9a2bcf8；本轮只动 src/app/ 内日志，不碰 mesh/model
下一步: 在 app_control_mesh_set_effect() 入口加日志打印当前 owner，上板发 0xC1，判断标准——若日志显示 owner != MESH 则假设成立
