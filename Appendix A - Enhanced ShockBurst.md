# Enhanced ShockBurst

## Transmiiting Payload

1. `PRIM_RX`要置为低电平
2. 当MCU有数据需要发送时，接收节点（`TX_ADDR`）和有效负载数据（`TX_PLD`）的地址需要通过SPI时钟输入nRF24L01
   - TX负载的宽度通过MCU写入 TX FIFO的字节数计算
   - `TX_PLD`必须在`CSN`保持低电平的时候连续写入
   - 如果`TX_ADDR`和上次发送时保持一致，则无需修改
   - 如果 PTX 设备应接收确认，数据管道0要设置为接收 ACK 数据包
   - 数据管道0的 RX 地址(`RX_ADDR_P0`)需要等于 PTX 设备中 TX 地址（`TX_ADDR`）
3. `CE`上的高脉冲开始传输，最小脉冲宽度是10$\mu s$
4. nRF24L01 ShockBurst$^{\text{TM}}$
   - 无线电已通电
   - 16MHz内部时钟启动
   - RF数据包完成（见数据包说明）
   - 数据高速传输(MCU配置1Mbps or 2Mbps)
5. 如果激活自动应答（`ENAA_P0=1`），无线电将立即进入RX模式，除非在接收到的数据包中设置了`NO_ACK`位
   - 如果在有效确认时间窗口内收到有效数据包，则认为传输成功
   - `STATUS`寄存器中的`TX_DS`位设置为高电平，并且有效负载从TX FIFO中删除
   - 如果在指定时间窗口内未收到有效的ACK数据包，则重传有效负载（如果启用了自动重传）
   - 如果自动重传计数器（`ARC_CNT`）超过编程的最大限制（ARC），`STATUS`寄存器中的`MAX_RT`位将设置为高。TX FIFOZ中的有效负载没有移除。
   - 当`MAX_RT`或`TX_DS`为高电平时，IRQ引脚有效
   - 要关闭**IRQ**引脚，必须通过写入`STATUS`寄存器来重置终端源
   - 如果在最大重传次数之后没有收到数据包的ACK数据包，则在清除`MAX_RT`中断之前不能再传输数据包
   - 丢包计数器（`PLOS_CNT`）在每次`MAX_RT`中断时递增，也就是说，`ARC_CNT`计算单个数据包通过所需的重传次数，`PLOS_CNT`计算再最大重传次数后未通过的数据包数量
6. 如果**CE**是低电平，则nRF24L01进入待机I模式。否则，发送TX FIFO中的下一个有效负载。如果TX FIFO为空，且**CE**仍为高电平，则nRF24L01进入待机II模式
7. 如果nRF24L01处于待机II模式，如果**CE**设置为低电平，他会立即进入待机I模式

## Receive Payload

1. 通过将`CONFIG`寄存器中的`PRIM_RX`设置为高来选择RX模式
   - 必须启用所有结束数据的数据管道（`EN_RXADDR`）
   - 必须启用所有所有运行增强型ShockBurst$^{\text{TM}}$
   - 必须设置正确的有效负载宽度（`RX_PW_Px`）
   - 地址必须按照上述增强型ShockBurst$^{\text{TM}}$所述进行设置
2. 通过将CE设置为高电平来启动RX模式
3. 130$\mu$s后，nRF24L01开始监控空中的传入通信
4. 当接受到有效数据包（匹配地址和正确的CRC）时，有效负载存储在RX-FIFO中，并且`STATUS`寄存器中的`RX_DR`位设置为高。
   - 当`RX_DR`为高时，IRQ引脚有效
   - `STATUS`寄存器中的`RX_P_NO`指示有效负载已在哪个数据管道中接收
5. 如果启用自动确认，则回发送回ACK数据包，除非在接收到的数据包中设置了`NO_ACK`位，如果`TX_PLD`FIFO中有有效负载，则该有效负载将添加到ACK数据包中
6. MCU通过设置**CE**低电平进入到standby-I模式
7. MCU可以通过SPI以合适的速率输出有效负载数据
8. nRF24L01现在已准备好进入TX或RX模式或掉电模式
