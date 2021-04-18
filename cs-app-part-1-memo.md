Part 1 - Program Structure and Execution


## 浮動小数点数について

## コンパイラの最適化 

## メモリ階層 p.294

In practice, a *memory system* is a hierarchy of storage devices with different capacities, costs, and access times. Registers in the CPU hold the most frequently used data. Small, fast caches memories near the CPU act as staging ares for a subset of the data and instructions stored in the relatively slow main memory.  The main memory stages data stored on large, slow disks, which in turn often serve as staging ares for data stored on the disks or tapes of other machines connected by networks.

レジスタは一番よく使われるデータを保持する.  
CPU 近くの高速なキャッシュメモリは一部のメインメモリに保存されるデータや命令のステージングエリアとなる.  
メインメモリはさらに低速で大容量なディスクのステージングエリアとなる.


## Storage Technologies

#### Random-Access Memory
*Random-access memory* (RAM) comes in two varieties -- *static* and *dynamic*. *Static RAM* (SRAM) is faster and significantly more expensive than *Dynamic RAM* (DRAM). SRAM is used for cache memories, both on and off the CPU chip. DRAM is used for the main memory plus the frame buffer of a graphic system. Typically, a desktop system will have no more than a few megabytes of SRAM, but hundreds or thousands of megabytes of DRAM.

- SRAM: 高速だが高額. メモリキャッシュに使われる. 
- DRAM: メインメモリに使われ、グラフィックのバッファとなる.


#### Static RAM

SRAM stores each bit in a *bistable* memory cell. Each cell is implemented with a six-transistor circuit. This circuit has the property that it can stay indefinitely in either of two different voltage configurations, or *states*. Any other state will be unstable -- starting from there, the circuit will quickly move toward one of the stable states.

bistable memory: 双安定のメモリ -> flip-flop

SRAM は記憶素子にフリップフロップ回路を利用.
- 定期的なリフレッシュ (回復動作) が不要
- 内部構造が複雑なため、高密度に実装できず、大容量メモリには向かないため、記憶容量あたりの単価が高い
- 高速な情報の出し入れが可能
- アイドル状態 () では低消費電力
- 制御が容易 (インターフェースが単純) で、より真の「ランダムアクセス性」があると言える
- 揮発性メモリ (volatile memory)
[Wikipedia より](https://ja.wikipedia.org/wiki/Static_Random_Access_Memory)  

参考: [論理回路: RSフリップフロップ回路](https://toshiba.semicon-storage.com/jp/semiconductor/knowledge/e-learning/micro-intro/chapter1/logic-circuit-rs-flip-flop-circuit.html)


The pendulum is stable when it is tilted either all the way to the left, or all the way to the right. From any other position, the pendulum will fall to one side or the other. In principle, the pendulum could also remain balanced in a vertical position indefinitely, but this state is *metastable* -- the smallest disturbance would make it start to fall, and once it fell it would never return to the vertical position.

pendulum: 振り子

#### Dynamic RAM

DRAM stores each bit as charge on a capacitor. DRAM storage can be made very dense -- each cell consists of a capacitor and a single-access transistor. Unlike SRAM, however, a DRAM memory cell is very sensitive to any disturbance. When the capacitor voltage is disturbed, it will never recover. Exposure to light rays will cause the capacitor voltages to change. 

DRAM は bit をキャパシタへの電荷の蓄積によって保存する. DRAM は SRAM と異なり、妨害に非常に弱い. キャパシタの電圧が改竄されると情報は戻らない (揮発性). 

Various sources of leakage current cause a DRAM cell to lose its charge within a time period of around 10 to 100 milliseconds. Fortunately, for computers operating with clock cycles times measured in nanoseconds, this retention time is quite long. The memory system must periodically refresh every bit of memory by words are encoded a few more bits (e.g., a 32-bit word might be encoded using 38 bits), such that circuitry can detect and correct any erroneous bit within a word.

様々な原因による漏れ電流によって、DRAM のセルはその電流蓄積を 10-100 ミリ秒で失う. しかし、コンピュータは ナノ秒単位でクロックするのでとてもその時間には猶予がある. 


```
// Characteristics of DRAM and SRAM memory

Transistors/bit: 
	SRAM-> 6
	DRAM-> 1

Relative access time: 
	SRAM-> 1x
	DRAM-> 10x

Persistent?:
	SRAM-> Yes
	DRAM-> No

Sensitive?:
	SRAM-> No
	DRAM-> Yes

Relative Cost:
	SRAM-> 100x
	DRAM-> 1x
```






