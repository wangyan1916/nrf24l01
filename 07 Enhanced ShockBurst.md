# 7 Enhanced ShockBurst

增强型 ShockBurst™ 是基于数据包的数据链路层。

它具有自动数据包组装和计时、自动确认和重新传输数据包的功能。

增强型 ShockBurst™ 可实现与低成本主机微控制器的超低功耗、高性能通信。

这些功能可显着提高双向和单向系统的电源效率，而不会增加主机控制器端的复杂性。

## 7.1 特性

增强型 ShockBurst™ 的主要特点是：

- 1 至 32 字节动态负载长度
- 自动数据包处理
- 自动数据包事务处理
  - 自动确认
  - 自动重传
- 用于 1:6 星形网络的 6 个数据管道 MultiCeiver™

## 7.2 增强型 ShockBurst™ 概述

增强型 ShockBurst™ 使用 ShockBurst™ 进行自动数据包处理和计时。

在传输过程中，ShockBurst™ 组装数据包并将数据包中的位计时到发送器中进行传输。

在接收过程中，ShockBurst™ 不断在解调信号中搜索有效地址。

- 当 ShockBurst™ 找到有效地址时，它会处理数据包的其余部分并通过 CRC 对其进行验证。
- 如果数据包有效，则有效负载将移至 RX FIFO 中。
- 高速位处理和定时由 ShockBurst™ 控制

增强型 ShockBurst™ 具有自动数据包事务处理功能，可实现可靠的双向数据链路。

增强型 ShockBurst™ 数据包事务是收发器之间的数据包交换，其中一个收发器是主接收器 (PRX)，另一个是主发送器 (PTX)。

增强型 ShockBurst™ 数据包事务始​​终由 PTX 的数据包传输发起，当 PTX 收到来自 PRX 的确认数据包（ACK 数据包）时事务完成