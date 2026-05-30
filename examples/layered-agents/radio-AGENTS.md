# radio 模块规则（示例）

> 放在 `src/radio/AGENTS.md`。只写 radio 模块特有的约束，通用规则继承根 `AGENTS.md`，不在这里重复。

## 模块职责

封装射频收发。对外只暴露 `radio_send(frame)` 和 `radio_register_rx_cb(cb)`，不暴露寄存器操作。上层不直接碰 PHY。

## 权威状态来源

链路状态以芯片状态寄存器 `RADIO->STATE` 的读回值为准，不以驱动内部缓存的 `s_link_state` 为准——后者是派生值，可能滞后。排查收发问题时先读寄存器。

## 协议帧格式（先定再改）

```
| preamble(1) | len(1) | addr(2) | payload(0..251) | crc(2) |
```

帧格式变更必须先更新本文件再改代码。len 不含 preamble 和 crc 自身。

## 时序约束

- TX 切 RX 的 turnaround 窗口 ≤ 192 µs，超出会丢首包 ack。
- 改 advertising/scan interval 属于 runtime 变量，一次只改一个，上板测丢包率验证。

## 验证方式

收发异常时的判断顺序：
1. 逻辑分析仪抓 SPI，确认帧真的发出去了（硬件实测，最高优先级）
2. RTT 看 `radio_send()` 返回码区分"没发/发了没 ack"
3. 读 `RADIO->STATE` 确认芯片当前在哪个状态

不要只看驱动里的计数器 marker 就下结论——那是派生日志，低于上面三项。
