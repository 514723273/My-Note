## 第 1 章：量化科普

一些概念：

- “量化交易”概念：针对可交易的投资产品，理性地运用**逻辑分析**和**归纳统计**~~（基本面、技术面）~~，判断市场趋势。
- 股票基金债券概念：
  - 股票：股份公司的所有凭证、盈亏自负
  - 基金：投资组合（股票、债券、现金）
  - 债券：还本付息的有价证券（国债、企业债、金融债券）
- 股票交易规则：
  - 开户：炒股平台、券商（交易费）
  - 交易版面：K 线、盘口、交易价格、数量（100 股）
  - 交易费：佣金（0.3%，最低 5 块钱（买少了亏））、过户费 1 元/千股、印花税 0.1%（1w，差不多要交 30 + 10 + 10 = 50 的交易费）
- 最大回撤：在“选定周期内”任一历史时点往后推，产品净值走到最低点时的收益率回撤幅度的最大值，即 (最低点-最高点) / 最高点；是一个非常重要的风险指标
- 夏普比率：代表投资者额外承受的每一单位风险所获得的额外收益（我承受了波动，是否能给我带来更多）；计算公式复杂



**量化策略**有啥（赚钱因子）？

- 基本面：居民消费指数、人均国内生产总值（GDP）、净资产收益率（ROE）
- 技术面：股票收盘价、K 线、均线



低买高卖，那如何判断买卖**时机**？看各种指标：

- 均线：择时策略
- 盈利能力：选股策略
- 周期性：板块轮动策略



相关平台：财经网站、证券公司（[深圳证券交易所](https://www.szse.cn/index/index.html)、[上海证券交易所](http://www.sse.com.cn/)）、炒股平台（[东方财富](https://www.eastmoney.com/)）

主要看：基本信息、财务数据、价格、成交、其他公告



Python 量化交易系统**模块拆分**：

- Data（行情记录与历史数据）
  - 指数：持仓个股、行情、资金流
  - 个股：基本信息、行情、资金流
- Strategy（交易策略与回测模块）
  - 择时策略：布林线、移动平均线、相对强弱指标
  - 选股策略：盈利能力、市值、估值
- Trade（交易数据管理与查询）
  - 委托、成交、持仓、资金
- Chart（图表可视化）



## 第 2 章：获取股票数据

### Resample 函数

Resample 函数：数据分析库 Pandas 的方法函数；应用于如果只知道日K数据，如何转换为周K；

```python
'''resampel 函数的使用：将日 K，转换为周 K'''

# from jqdatasdk import *
import jqdatasdk as jq
import pandas as pd

jq.auth('18668014061', 'Yqb19960122')

# 设置行列不忽略（在 print 中，不以省略号的形式展示）
pd.set_option('display.max_rows', 100000)
pd.set_option('display.max_columns', 10)

# 转换周期：日K转换为周K
df = jq.get_price('000001.XSHE', count=22, end_date='2021-10-01', frequency='daily', panel=False) #获取日K

# print(df.index.weekday) # Int64Index([2, 3, 4, 0, 1, 2, 3, 4, 0, 1, 2, 3, 4, 2, 3, 4, 0, 1, 2, 3], dtype='int64')
df['weekday'] = df.index.weekday # 只是用于自查
print(df)

# 转换周期：日 K 转换为周 K
# 也就是获得当周的：开盘价（当周第一天）、收盘价（当周最后一天）、最高价、最低价
df_week = pd.DataFrame()
df_week['open'] = df['open'].resample('W').first()
df_week['close'] = df['close'].resample('W').last()
df_week['high'] = df['high'].resample('W').max()
df_week['low'] = df['low'].resample('W').min()

# 汇总统计：统计一下周成交量、成交额（sum）
df_week['volume(sum)'] = df['volume'].resample('W').sum()
df_week['money(sum)'] = df['money'].resample('W').sum()

print(df_week)
```

### 查询财务指标

一家企业的财务报表的构成：

- 资产负债表：财务状况，体现企业家底和负债情况；包括资产、负债、所有者利益
- 利润表：经营成果，公司盈利能力、赚了多少、怎么赚的，隐含着对未来利润增长的预期，**体现市场空间、成长能力**；包括收入、成本费用、利润；容易造假（权责发生制）
- 现金流量表：现金流量，不容易造假（收付实现制），包括现金流入、现金流出

```python
'''获取股票财务指标'''

fundamentals = jq.get_fundamentals(jq.query(jq.indicator), statDate='2020') # 获取财务指标数据
fundamentals.to_csv('./Data/finance/finance2020.csv') # 因为 print 不好看，导出到 csv

# 基于盈利指标选股
fundamentals = fundamentals[(fundamentals['eps'] > 0) & (fundamentals['roe'] > 11) & (fundamentals['operating_profit'] > 2212173617)] # 筛选股票

print(fundamentals) # 查看结果
```

### 查询估值指标

```python
fundamentals.index = fundamentals['code']
# print(fundamentals)

'''获取股票估值指标'''
valuation = jq.get_fundamentals(jq.query(jq.valuation), statDate=datetime.datetime.today())
valuation.index = valuation['code']

fundamentals['pe_ratio'] = valuation['pe_ratio']
print(fundamentals)
```

### data/stock.py

封装了之前提到的部分函数：

```python
# 获取所有 A 股股票列表
def get_stock_list()

# 获取单个股票行情数据
def get_single_stock_pric(code, timeperiod, start_date, end_date)

# 导出股票行情数据
def export_data(data, filename, type)

# 转换股票行情周期
def transfer_price_freq(data, time_freq)

# 获取单个股票财务指标
def get_single_finance(code, date, statDate)

# 获取单个股票估值指标
def get_single_valuation(code, date, statDate)

# 计算涨跌幅
# 涨跌幅 = (当期收盘价 - 前期收盘价) / 前期收盘价
def calculate_change_pct(data)
```

### 调用函数

章节的最后，就是写了一个例子，调用上面的函数，存储到 csv 文件中；做到每日更新；

## 第3章：计算交易指标

- 创建了 Strategy 模块
- 简单地模拟了在周一买入周四卖出（中间整合了信号，防止连着两天买入卖出）
- 计算了单次卖出的收益率；也很简单，就是（平仓-开仓）/ 开仓
- 计算累计收益率，见代码
- 计算最大回撤比，见代码
- 计算夏普比，见代码

### strategy.py

```python
# 利用单次收益率，计算累计收益率
# (1 + 第 1 天收益率) * (1 + 第 2 天收益率) * ... * (1 + 第 n 天收益率) - 1
def calculate_cum_prof(data):
  	data['cum_profit'] = pd.DataFrame(1+data['profit_pct']).cumprod() - 1
    return data
  
# 计算最大回撤比
def caculate_max_drawdown(data):
  # 1. 选取时间周期
  # 2. 选取时间周期中的最大净值
  # 3. 计算当天的回撤比 = (谷值 - 峰值) / 峰值
  # 4. 选取时间周期内最大的回撤比，即最大回撤
  
# 计算夏普比
def calculate_sharp(data):
  # 公式：sharp = (回报率的均值 - 无风险利率) / 回报率的标准差
  # 因子项1: 回报率的均值 = 日涨跌幅.mean()
  # 因子项2: 0 或者 3%，国债这些稳定收益
  # 因子项3: 回报率的标准差 = 日涨跌幅.std()
```

## 第 4 章：设计交易策略——择时策略

### 数据准备

- 将数据保存至本地（.csv）
- 获取本地数据

### 双均线策略

均线概念：代表过去 N 日估价平均走势；例如 5 日均线 = (第 1 天价格 + ... + 第 5 天价格) / 5 天。

基础操作：金叉买入，死叉卖出。

实现思路：

- 获取标的行情
- 计算技术指标：移动平均线 5 日、10 日
- 生成交易信号：金叉则买入、死叉卖出
- 计算收益率：单次收益率、累计收益率
- 寻找最优参数：均线周期、投资标的（遍历所有的双均线（5-10、5-30、5-250...120-250）结果、遍历多个股票，收益率从大到小）
- 与市场基准比较：沪深300、上证、中证500
- 策略评估：收益、夏普、波动率、回撤、胜率
- 假设检验：t-test；没懂；p 值 < 显著性水平 α，则拒绝原假设
- 具体实现在：4-5

## 第5章：设计交易策略——选股策略

概述：讲的就是如何组合选股——在一个月末买入哪些股票，卖出哪些股票。

动量策略：预先对股票的**收益**和**交易量**设定过滤准则，当“股票收益”或“股票收益和交易量同时”满足过滤准则就买入（做多）或者卖出（做空）股票的投资策略。

设计思路：

- 正向策略：涨的还会涨、跌的还会跌；买入涨最多的、卖出跌最多的；利用市场对信息的反应不足；
- 反向策略：涨太多会跌、跌太多会涨；买入跌最多的、卖出涨最多的；市场对信息反应过度；

实现步骤：

1. 确定交易对象（股票池）：
   - 考虑流动性（沪深 300、创业板）（流动性主要是为了交易顺畅）；
   - 考虑基本面（行业、应收、盈利增速、现金流、负债）；
   - 标的价格（在意一手要多少钱）；
2. 选定业绩评价周期；可能是 1 个月内的、2 个月内的，随意；
3. 计算动量因子：
   1. 明确计算公式：简单收益率；
   2. 设计业绩评价周期：默认是 1 个月；
   3. concat 先**横向**排列所有沪深 300 股票（纵轴是时间（隔一天），横轴是股票名称，中间的每一个值都是对应股票当前日期的收益率）；
   4. 计算动量——*过去 N 个月的收益率 = (期末 - 期初) / 期初*；例如 N = 10，期初是 1 月收盘价，期末是 11 月收盘价；需要使用 resample 函数转换“日-月”周期；
4. 对收益率进行排序：最佳-赢家组合、最差-输家组合；
   - 将一行（即一个月）的收益率从小到大排序（row.nlargest 找到极值），前 3 个即为赢家组合，最后 3 个即为输家组合；
   - 赢家组合买入 1，输家组合卖出 -1；
5. 确定持仓 / 换仓周期：可以为 1 个月；可自定义测算；
6. 连续或间隔一段时间，不断重复 2-5 行为；
7. 计算动量 / 反向策略各持有期的回报率；
   - 计算组合收益率（总收益 / 总本金）；
8. 计算 t/p 统计值，判断是否存在动量效应；

## 第6章：PyAlgoTrade 数据回测与优化

概念：

- 回测：一种反向测试，旨在估计策略或模型的在过于一段时间内的表现
- 回测与实盘的差异：未来函数、滑点——导致交易价格与策略价格不一致

常用的数据回测框架：

- ZipLine、PyAlgoTrade（简单、好上手）、BackTrader

本章节，主要是介绍 PyAlgoTrade 的基础使用，使用现有的框架重复实现前面章节的内容。

## 第7章：实现股票实盘交易

程序化交易的方式：

- ×对接券商：发送至交易所、授权信息、交易和查询功能
- ×交易柜台：宽睿、中泰、华鑫、华锐、恒生，面向机构
- ×三方平台：万得、彭博，主流量化平台
- 个人投资者：网页模拟登录、基于 exe 接口（**EasyTrader** 封装使用）

EasyTrader 工作原理：利用程序模拟操作 win 图形化界面（安装同花顺）（pywin32、pyautogui）；

本章节，主要是使用 EasyTrader 替换掉之前的交易信号（1、-1），完成真正的自动化交易。

最后介绍了一些其他的股票因子，~~还有其他方法的探讨的地方——研报里搜因子，例如 NLP 分析新闻~~。