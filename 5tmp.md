# System Performance Chap-5


### 5.2.5 Concurrency and Parallelism

Unixを含むタイムシェアリングシステムでは、プログラムの並行性を提供している。  
並行性とは複数の実行可能なプログラムを読み込み実行を開始できることである。  
これらの実行される時間は重複する一方で、必ずしもCPU上で同時に実行されていなければならないということではない。  
マルチスレッドやマルチプロセスによって異なるアプリケーション同士の並行性や同じアプリケーション内の異なる関数での並行性がある。  
また、他のアプローチとして、event-based並行性もある。  
これによって、イベント駆動で実行する関数を切り替えることができる。  例えば、Node.jsがこの方法をとっている。  
event-basedによって並行性を提供する個tができるが、これは徐々にボトルネックになるであろうシングルスレッドやシングルプロセスによって実現されている。  

マルチプロセッサの利点を活かすために、アプリケーションは同時に複数のCPUを実行するべきである。  
これが、アプリケーションがマルチプロセスやマルチスレッドになることに酔って達成される、並列性である。  
この理由は6章CPUでされる。

CPUのスループットの増加の他に、マルチスレッド・マルチプロセスはI/Oの並行実行を可能にする。  

マルチスレッドプログラミングでは、同じアドレス空間を共有しているため、スレッドは同じメモリ空間を読みだしたり、書き込んだりすることが可能である。  
一方で、完全性のために、同期プリミティブを使って、同時に読み出しや書き込みが発生してもデータが壊れないようにする必要がある。  
これらはパフォーマンスを向上するためにハッシュテーブルを使って行われる。


[Synchronization Primitives]

同期プリミティブは、道路における信号機のようにメモリへのアクセスを管理する。  
よく使われるのは以下の3つである。

- Mutex(MUTually EXclusive) locks
- Spin locks
- RW locks

Mutex lockは"adaptive mutex locks"としてライブラリかカーネルに実装されている。  
adaptive mutex lockは、他のCPUで実行されている場合はspinロックをおこない、そうでない場合やspinロックのスレッシュホールドに達した場合は、単純なmutexロックを行うというものである。  
Adaptive mutex locksはCPUリソースな無駄な消費をせずに低レイテンシを実現するために、Solarisによって長年使われてきた。  
Linuxでは"adaptive spinning mutexes"として2009年から実装されている。


[Hash Tables]

ハッシュテーブルは巨大なデータ構造に対して、ロックを最適な数だけ取得するために使われる。  
ハッシュテーブルを使わない場合の巨大なデータ構造に対するロックのアプローチは以下の2つが考えられる。  

- １つのグローバルロック
  - この方法はシンプルだが、複数のアクセスがロックの部分で直列化され、パフォーマンスをおおきく損なう
- データ構造ごとのロック
  - データ構造ごとにロックを使うので、必要な部分だけでロックのチェックが行われる
  - しかし、ロック機構が増えることによる、ストレージのオーバーヘッド、ロック機構を作ったり壊したりすることによるCPUのオーバヘッドが増えることになる

ハッシュテーブルによるロックは、競合が少ないと予想される場合の、上記の中間に当たる解決法である。  
まず、一定数のロック機構を作る。それに対して、ハッシュアルゴリズムによって、どのロック機構がどのデータ構造に使用されているかを選択する。  
これによって、ロック機構をデータ構造ごとに構築、破壊するコストをさけることができる。  

図5.2にハッシュテーブルの例を示す。  

この例ではハッシュのキーが重複した場合の例も示している。  
キーが重複した場合は、キーに対してデータチェインを作って順に格納するようにしている。  
このハッシュチェインは重複するデータが増えると重大なパフォーマンス低下につながる。そのため、ハッシュ関数とテーブルサイズはハッシュチェインを短くするように選択する必要がある。  

理想的には、ハッシュのキーサイズは並列数の最大化のためにCPUの数と同じかそれ以上になることが好ましい。




.