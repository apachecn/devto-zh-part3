# 优化 ruby CLI 工具的启动时间

> 原文：<https://dev.to/okinawarb/optimize-boot-time-of-ruby-cli-tools-4a7l>

创建一个 binstub，如下所示:

```
#!/usr/bin/env ruby
require 'bootsnap'
gem_name = 'rubocop'
exe_name = 'rubocop'
cache_dir = File.join(ENV['HOME'], '.cache', 'bootsnap', gem_name, exe_name)
Bootsnap.setup(cache_dir: cache_dir, load_path_cache: false, autoload_paths_cache: false)
load Gem.bin_path(gem_name, exe_name) 
```

`Gem.bin_path` <sup id="fnref1">[1](#fn1)</sup> 带两个参数，第一个是 gem 的名称，第二个是可执行文件的名称。您可以修改`gem_name` / `exe_name`来将这个 binstub 应用到其他 ruby CLI 工具。

没有这个 binstub:

```
% time rubocop -v
0.70.0
rubocop -v  0.76s user 0.20s system 97% cpu 0.983 total 
```

有了这个 binstub，第一次运行时会多花一点时间:

```
% time bin/rubocop -v
0.70.0
bin/rubocop -v  1.04s user 0.57s system 98% cpu 1.640 total 
```

但是一旦它运行，缓存就被创建了。然后就变得快多了:

```
% time bin/rubocop -v
0.70.0
bin/rubocop -v  0.53s user 0.23s system 79% cpu 0.969 total 
```

你不需要改变 ruby CLI 工具的任何代码，只需要创建一个 binstub 来优化启动时间。

干杯< 3

* * *

1.  [https://www . ruby doc . info/github/ruby gems/ruby gems/gem . bin _ path](https://www.rubydoc.info/github/rubygems/rubygems/Gem.bin_path)↩