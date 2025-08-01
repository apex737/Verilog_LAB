### <AHB: Advanced High Performance Bus>
### 1) 2-phase (addr, data)
- __addr phase__: Set __addr, sel, write__ signals
- __data phase__: Transfer Data
- __2-phase__ 구조를 통해 data-pipelining과 multi-transfer을 구현 가능

### 2) Additional Signals ( APB+ )
#### < Burst-Mode >
* __HTRANS[1:0]__ (state)
  * __IDLE__ 
  * __BUSY__  : Burst Transfer 중 인터럽트 
  * __NONSEQ__: 단일 데이터 또는 burst의 첫 전송
  * __SEQ__   : 연속 데이터 전송
   
* __HBURST[2:0]__ (mode select)
  * __Case__
    * 0: 단일 데이터
    * 1: N-연속(beat) 순차 전송
    * 2/3: 4-beat wrap/순차 전송
    * 4/5: 8-beat wrap/순차 전송
    * 6/7: 16-beat wrap/순차 전송
  * __Wrap__
    * 순환 버퍼의 Boundary를 지정하여 그 내부에서 주소를 증가하면서 접근
    * Boundary = HSIZE * HBURST_beat
    * Ex. HSize = 8Byte, HBURST = 2 (4-beat), StartAddr = 0x3C
      * Boundary  = 8 * 4 = 32Byte
      * Range     = [0x00 ~ 0x1F], [0x20 ~ 0x3F], [0x40 ~ 0x5F]...
      * HAddr     = 0x3C -> ~0x44~ -> 0x24 -> 0x3C ...
  
* __HSIZE[2:0]__: 클럭 당 __주소 증가__ size

#### Arbiter 
> 운영체제의 CPU 스케쥴링, 동기화 개념이 사용됨
* __HMASTER__: 버스 소유 마스터
* __HBUSREQ__: 마스터가 아비터에게 버스 소유권 요청
* __HGRANTx__: 아비터가 우선순위가 가장 높은 마스터에게 소유권 부여
* __HSPLITx__: Slave가 Master에게 Busy임을 알리는 신호; Master에게 Bus 소유권을 포기하도록 하여 버스 병목 방지
* __HLOCKx__ : 마스터가 버스 소유권을 LOCK
* __HMASTLOCK__: 버스가 LOCK된 것을 다른 마스터에게 알림 

