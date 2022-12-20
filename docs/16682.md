# 实施监控/随叫随到审计和补救

> 原文：<https://dev.to/jmarhee/implementing-monitoringon-call-audit-and-remediation-44p9>

如果你曾经参与过随叫随到的工作，你可能会知道这可能会有点麻烦，尤其是当你的警觉性有点太好的时候；你每晚醒来 5 次，看到的页面似乎在你刚刚足够清醒时就解决了，以确保你不容易再次睡着，也许经常发生的是，当一个重要的页面出现时，你没有足够快地抓住它，并且**威猛**你有一个事件回顾要为第二天做准备。

当然，为您的随叫随到创建一个迭代的、审计驱动的、可测试的程序是很费力的，但是回报是您获得了更好的数据、更有用的上下文、更少的响应噪音的时间，以及更多的先发制人地识别信号的时间。

## 对覆盖率进行审计

这一过程的第一步是了解什么被监控，什么没有被监控。观察到哪些宿主？观察哪些服务？每个服务都有主机吗？问题没完没了。

我试图通过询问我的真理之源来回答这个问题，在这种情况下，是纳吉奥斯。

我编写了一个脚本来准备一个主机监视器的电子表格，以及一个服务监视器表，该表指示是否存在到被监视主机的映射:

```
#!/bin/bash

hosts () {
  cat /etc/nagios/conf.d/hosts/*.cfg | grep "host_name\|address\|alias\|hostgroups" |grep -v localhost | perl -ne '$line = $_;
  chomp($line);
  if ($line =~ /host_name(.*)/) {
  $match = $1 ;
  $match =~ s/ |,//g;
  print "\n".$match.",";
  };
  if ($line =~ /address(.*)/) {
  $match = $1 ;
  $match =~ s/ |,//g;
  print $match.",";
  }
  if ($line =~ /alias(.*)/) {
  $match = $1 ;
  $match =~ s/^\s+//;
  $match =~ s/,//g;
  print $match.",";
  }
  if ($line =~ /hostgroups(.*)/) {
  $match = $1 ;
  $match =~ s/^\s+//;
  $match =~ s/,//g;
  print $match.",";
  };
  '
}

services () {
  cat /etc/nagios/conf.d/services/*.cfg | grep "hostgroup_name\|service_description\|check_command" |grep -v localhost | perl -ne '$line = $_;
  chomp($line);
  if ($line =~ /hostgroup_name(.*)/) {
  $match = $1 ;
  $match =~ s/ |,//g;
  print "\n".$match.",";
  };
  if ($line =~ /service_description(.*)/) {
  $match = $1 ;
  $match =~ s/ |,//g;
  print $match.",";
  }
  if ($line =~ /check_command(.*)/) {
  $match = $1 ;
  $match =~ s/ |,//g;
  print $match.",";
  };
  '
}

write_inv () {

  if [ $1 = "dump" ]; then
    if [ $2 = "hosts" ]]; then hosts
    elif [ $2 = "services" ]; then services
    else echo "bad option (services | hosts)"
    fi
  elif [ $1 = "audit" ]; then DATE=`date +%Y%m%d%H%m`
    OUTFILE_hosts="$HOME/hosts_$DATE.csv"
    OUTFILE_services="$HOME/services_$DATE.csv"

    echo "hostgroup_name,service_description,check_command," >> $OUTFILE_services && \
    echo "Writing $OUTFILE_services..." && \
    services >> $OUTFILE_services

    echo "hostname,address,hostgroups," >> $OUTFILE_hosts && \
    echo "Writing $OUTFILE_hosts..." && \
    hosts >> $OUTFILE_hosts
  else echo "Options: { dump (prints to stdout) [hosts, services] | audit (writes to file) }"
  fi
}

main () {
  write_inv $@
}

main $@ 
```

因此，运行`./audit.sh audit`将创建那对 CSV，在`service`视图中，它将为您提供主机组、服务以及服务中测试的内容(这是 nagios 中的一个模块的路径，因此您可以确保哪些监视器正在使用)，然后 host_group 的使用情况可以与被监视的主机列表进行交叉检查(这将告诉您哪些主机组成了那个组)。

这将告诉你两件事:

*   监控的是什么

和

*   它的监测情况如何(覆盖率)

然而，仅仅这种背景不会解决你所有的问题；这将告诉您某些东西是否被监控，而不是该监控是否值得您花费时间。你可以辨认出是少了什么东西，但不是没有对准。

例如，在数据库服务器上，您可能正在监视死锁，但是如果实例已经着火(网络中断、复制插槽故障、实例可能已被终止并且上游 libvirt 检查可能没有发现它，等等),那么这将没有什么好处。).您可以填补这些空白，但是在您的审计周期内(即每季度)，您可以跟踪和自动记录您团队的响应，并获得一些额外的见解。

## 警惕疲劳！

改进监控的关键是了解警报来自哪里，并评估响应是否相称。

如果您知道某件事情将会发生，并且您了解它发生的原因和方式，它会在一段时间内自行解决，并且您会定期确认该问题的页面，但在您在线检查之前发现它已经解决，那么这可能是可以自动消除的事情，只有当问题持续存在时才会升级(即使升级路径会增加几秒钟，以表明它不能单独通过自动化解决)。

大多数呼叫寻呼机服务都有这方面的插件，但我想说的是审核您的响应以及识别错位的显示器。

因为我喜欢 speadsheets，所以我定期运行这样一个脚本，以查看在随叫随到期间我们花费最多时间响应的内容:

```
import requests
import json
import os
import datetime
import sys
import optparse

p = optparse.OptionParser(conflict_handler="resolve", description= "Creates CSV for PagerDuty alert history; requires start and end date.")
p.add_option('-s', '--since', action='store', type='string', dest='since', default='', help='Start date for reporting')
p.add_option('-u', '--until', action='store', type='string', dest='until', default='', help='End date for Reporting')
p.add_option('-k', '--key', action='store', type='string', dest='api_key', default='', help='PagerDuty API Key')
options, arguments = p.parse_args()

since = options.since
until = options.until
key = options.key

if 'key' in locals():
    API_KEY = key
else:
    API_KEY = os.environ['PAGERDUTY_RO_KEY']

SINCE = since
UNTIL = until
STATUSES = []
TIME_ZONE = 'UTC'
LIMIT = 50
RUNDATE = datetime.datetime.today().strftime('%Y%m%d%H%M%S')

def list_incidents(offsetval):
    url = 'https://api.pagerduty.com/incidents'
    headers = {
        'Accept': 'application/vnd.pagerduty+json;version=2',
        'Authorization': 'Token token={token}'.format(token=API_KEY)
    }
    payload = {
        'since': SINCE,
        'until': UNTIL,
        'statuses[]': STATUSES,
        'limit': LIMIT,
        'time_zone': TIME_ZONE,
        'offset': offsetval
    }
    r = requests.get(url, headers=headers, params=payload)
    return r.text

def write_csv(resp):
    incidents = json.loads(resp)['incidents']
    incidents_data = open('%s/pd-audit/%s-Incidents-%s.csv' % (os.path.expanduser('~'), RUNDATE, offset), 'w+')
    for inc in incidents:
        incidents_data.write("%s,%s,\n" % (inc['title'],inc['created_at']))
    incidents_data.close()

if __name__ == '__main__':
    more_status = True
    offset = 0
    while more_status == True:
        resp = list_incidents(offset)
        more = json.loads(resp)['more']
        if more == False:
            more_status = False
            print "No more pages after current run. '%s/pd-audit/%s-Incidents-%s.csv'..." % (os.path.expanduser('~'), RUNDATE, offset)
            write_csv(resp)
        else:
            print "Writing '%s/pd-audit/%s-Incidents-%s.csv'..." % (os.path.expanduser('~'), RUNDATE, offset)
            resp = list_incidents(offset)
            write_csv(resp)
        offset += 1 
```

你可以根据事件类型对表格进行分类——有多少是我手动干预的结果？有多少不必要的页面得到了足够快的解决，从而证明了使用 bot 进行即时确认的合理性？有多少人会因为我们能早点发现而变得更好？(还记得那个数据库例子吗？)本可以发生什么*不同的事情*来使我们实际上*做得*更好？问题的清单还在继续。

这些练习的要点是，在你的脑海中，什么对你和你的团队有用，什么没用。这让你在你和你实际回应的东西之间有了一些客观的距离，通过数据来说一些事情被证明是有帮助的，或者被证明是一种拖累。

通过实施这一迭代过程，每一遍都可以让您更深入地了解您的团队在救火时可以花费更少的时间来投资，或者，就我而言，从这些监控盲点中撤资