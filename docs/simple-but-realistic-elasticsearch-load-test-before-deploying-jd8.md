# 部署前简单但现实的弹性搜索负载测试

> 原文：<https://dev.to/zealot128/simple-but-realistic-elasticsearch-load-test-before-deploying-jd8>

在迁移、升级或类似操作之前对 Elasticsearch 集群进行负载测试总是减少事后不良事件的好策略。从 2014 年开始，我们使用 Elasticsearch 作为我们所有(德国)社区网站(Empfehlungsbund)的工作搜索后端，以及博客文章搜索和关键字优化。

最近，我准备了这样一个迁移，并且需要一种方法来验证集群在切换后保持不变，更好的是，在成本方面提高性能。在检查了 Github et 之后。艾尔。对于其他特定的代码，我还不够满意，并围绕令人敬畏的`siege`工具构建了一些小脚本。

## 准备-为测试收集好查询

为了获得真实的性能测试，我建议获取您的生产 es 运行的查询的原始负载。更重要的是，我只选择了几个最慢的查询来增强我的信心。

要做到这一点，首先在你的 UI (cerebro/kopf 无论什么前端)的 ES 设置中启用`Slow Log`或者通过 curl:

```
curl 'http://localhost:9200/index_settings/update' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json;charset=utf-8' --data \
  '{"index":"CLUSTERNAME","settings":{"index.search.slowlog.threshold.query.warn":"1s","index.search.slowlog.threshold.query.info":"0.5s"},"host":"http://localhost:9200"}' 
```

Enter fullscreen mode Exit fullscreen mode

然后，等待一段时间或通过查询生成缓慢的日志。之后，文件`/var/log/elasticsearch/*_index_search_slowlog.log`将被填满。

通过复制括号中的所有 json 来提取查询负载:

```
[2019-12-11 04:30:29,725][INFO][index.search.slowlog.query] [es01.localhost] [ebsearch_production][2] took[1.7s],
  took_millis[1774], types[], stats[], search_type[QUERY_THEN_FETCH], total_shards[5], source[<>], extra_source[], 
```

Enter fullscreen mode Exit fullscreen mode

*   将每个有效载荷分别放在一个公共文件夹的单独文件中，例如`payloads/1`、`payloads/2`等。

## 建立新的测试集群

一个提示(如果尚未使用):使用存储库+快照(S3)快速为新集群播种生产级数据。

## 测试(旧/新)集群

测试由久经考验的工具**围攻**运行，它应该很容易从你的所有操作系统 repo (Apt，Brew 等)安装。).Siege 支持一个带有要测试的 URL 的文件的输入参数。后来，我们就这样利用围攻:

```
# Concurrency: 3, for 1 minute
siege -b --log=./siege.log -H 'Content-Type: application/json' \ 
  --internet --delay=15 -c 3 -t 1M --file=urls.txt 
```

Enter fullscreen mode Exit fullscreen mode

urls.txt 的格式为:

```
http://server/index/_search POST {".....search payload"} 
```

Enter fullscreen mode Exit fullscreen mode

为了用所有的有效负载轻松地生成`urls.txt`，我创建了一个`Rakefile`，因为 Ruby 太棒了。此外，我们生活在 2019 年(+)，所以你的发行版附带的 Ruby 应该没问题，不需要 rvm/rbenv。

```
SERVER = '10.10.10.100:9200'.freeze
DURATION = '1M'.freeze # 1 minute each test
CONCURRENCY_TESTS = (1..10) # or [1, 5, 10, 20, 100] etc.
INDEX_NAME = 'ebsearch_production'

desc 'create urls.text file with all payloads in payloads/*'
task :urls do
  out = Dir['payloads/*'].map do |pl|
    "http://#{SERVER}/#{INDEX_NAME}/_search POST #{File.read(pl)}"
  end
  puts "recreating urls.txt for #{SERVER} with #{out.count} requests"
  File.write('urls.txt', out.join("\n"))
end

desc 'run series!'
task :run do
  File.unlink('siege.log')
  (1..MAX_CONCURRENCY).each do |c|
    puts "==== #{c} Concurrent ==== "

    sh %[siege -b -m "#{SERVER}-C#{c}" --log=./siege.log -H 'Content-Type: application/json' --internet --delay=15 -c #{c} -t #{DURATION} --file=urls.txt]
  end
end

desc 'show csv as tsv for copy paste into google spreadsheets'
task :csv do
  lines = File.read('siege.log').lines
  csv = lines.reject { |i| i.include?(" ****") }.map { |line| line.gsub(',', '').gsub('.', ',') }.join
  puts csv
end

task default: [:urls, :run] 
```

Enter fullscreen mode Exit fullscreen mode

*   修改文件头中的参数
*   运行它！`rake`
*   完成后(CONCURRENCY_TESTS * DURATION)，您可以输出数据:`rake csv`并将输出复制到例如 Google 电子表格中，以便轻松生成图表

## 要点:带 ascii 字符的快速图表

安装捆扎机，如果尚未安装`gem install bundler`

追加到 Rakefile:

```
require 'bundler/inline'
gemfile do
  source 'https://rubygems.org'
  gem 'ascii_charts'
end
desc 'chart'
task :chart do
  require 'ascii_charts'
  lines = File.read('siege.log').lines
  csv = lines.reject { |i| i.include?(" ****") || i.include?('Elap Time') }
  data = csv.map { |i| i.split(',').map(&:strip) }.map { |a| %w[date transactions duration transfer response_time requests_s mbs conc success failed].zip(a).to_h }
  require 'pry'
  puts "======= Response Time / Concurrency ========"
  items = data.each_with_index.map { |d| [d['conc'].to_f.round, d['response_time'].to_f] }
  puts AsciiCharts::Cartesian.new(items).draw

  puts "======= Requests/s / Concurrency ========"
  items = data.each_with_index.map { |d| [d['conc'].to_f.round, d['requests_s'].to_f] }
  puts AsciiCharts::Cartesian.new(items).draw
end 
```

Enter fullscreen mode Exit fullscreen mode

## 趣闻:我们小小星团的结果

我们的搜索使用了定制的搜索插件，这些插件占用了大量的 CPU 资源，尤其是长时间的查询。总的来说，我们的并发用户并不多，所以 3-4 个节点的集群一般就足够了。

部署目标是非常经济高效的 Hetzner 云(HCloud)。以下是 Hetzner 目前(2019 年)提供的不同云实例类型的快速概述:

*   CX11 (1 个内核，2GB，3 欧元)
*   CX21(双核、4GB、6 欧元)
*   CX31(双核、8GB、11 欧元)(不包括在内，因为没有改进 CPU，也没有利用 RAM)
*   CX41 (4 核，16GB，19 欧元)
*   CX51 (8 核，32GB，36 欧元)

我在 Hetzner cloud 中尝试了以下组合。请注意，如果 rq/s 看起来低得离谱，但请记住，这 10 个并发用户只是在搜索我能找到的最差的查询。

| 节点 | 欧元/月 | rq/s @ 10ccu | 10 ccu 时的响应时间 | 请求/秒/欧元 |
| --- | --- | --- | --- | --- |
| 1x 协调器(CX11) + 2x 数据 CX21 | 15 欧元 | Five point nine | One point six seven | Zero point three nine |
| 1 个协调器(CX11)+3 个数据 CX21 | 21 欧元 | Four point eight four | Two point zero three | Zero point two three |
| 1 个协调器(CX11)+2 个数据 CX41 | 41 欧元 | Eleven point two four | Zero point eight | Zero point two seven |
| 1 个协调器(CX11)+3 个数据 CX41 | 60 欧元 | Seventeen point one nine | Zero point five eight | Zero point two eight |
| 1 个协调器(CX11)+4 个数据 CX41 | 79 欧元 | Sixteen point five six | Zero point five four | Zero point two |

我的发现:

*   即使最小的实例对协调器节点来说似乎也没什么问题，CPU 使用率从未达到任何种类的利用率
*   从 2 CX21 升级到 3 CX21 并没有改善核心指标(请求数/秒、响应时间)，反而使其恶化。我的结论是，CX21 的 CPU 能力太低
*   一样，去 4 CX41 好像比 3 CX41 差
*   2 或 3 个 CX41 性价比最高
*   CX51 未经测试
*   请注意:这些发现可能完全与我们的查询类型有关，包括用 Groovy 编写的自定义搜索算法，此外:我不是弹性搜索专家，所以可能需要调整参数和分片设置。