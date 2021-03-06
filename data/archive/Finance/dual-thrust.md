# Dual Thrust 策略


量化系统：
  * 策略系统
    - 条件（价格、资产、时间）=> 动作（买入、卖出）
  * 交易系统
    - 交易账号验证与账号授权
    - 资金账号对接
    - 交易接口网络请求
    - 异常处理
    - 自动排队和重试
    - 确认交易结果
    - 同步账号信息
    - 机器容灾，风险控制，人工介入
  * 情报系统



短期趋势突破策略。

趋势跟踪

追涨杀跌

Dual Thrust是一个趋势跟踪系统，由Michael Chalek在20世纪80年代开发，曾被Future Thruth杂志评为最赚钱的策略之一。
Dual Trust是一个追涨杀跌的策略，原理并不复杂。是一个简单而又有效的短期趋势策略。

Dual Thrust策略利用前N日的最高价，最低价和收盘价，来确定一个合理的震荡区间Range。
利用前一时间点的开盘价和Range，确定当前的上下双轨。如果当前价格向上/向下突破Range一定的比例，则认为一波上涨/下跌行情形成，产生买入/卖出信号。


（1）N日内每天的最高价HIGH(N), 最低价LOW(N), 和收盘价CLOSE(N)
（2）HH = N日HIGH的最高价 = MAX(HIGH(N))
        LC = N日CLOSE的最低价 = MIN(CLOSE(N))
        HC = N日CLOSE的最高价 = MAX(CLOSE(N))
        LL = N日LOW的最低价 = MIN(LOW(N))
（3）计算震荡区间Range
        Range = MAX（HH-LC, HC - LL）
（4）计算上下轨
        上轨 = 开盘价 + K1 * Range
        下轨 = 开盘价 - K2 * Range
        K1, K2为Range的系数，由用户自行设定。


## 使用方法

Dual Thrust策略是一个趋势突破策略。计算好上下轨之后，一旦价格突破上轨，我们就买入；当价格突破下轨，我们就卖出。所以，对于上下轨系数的设定十分关键。

K1和K2的值越小，越容易触发对应的轨道。而且，当K1<K2时，多头相对容易被触发；当K1>K2时，空头相对容易被触发。
因此，投资者在使用该策略时，一方面可以参考历史数据测试的最优参数，另一方面，则可以根据自己对后市的判断，或从其他大周期的技术指标入手，阶段性地动态调整K1和K2的值。

* http://www.cnblogs.com/fangbei/p/wequant-strategy-Dual-Thrust.html
* 中低频量化交易策略研发
