# Cloudflare 动态 DNS 更新程序

> 原文：<https://dev.to/jsrnop/cloudflare-dynamic-dns-updater-o4m>

这是我写的一个快速 Python 脚本，当提供了您的 Cloudflare 帐户电子邮件地址和 API 令牌时，它可以为您更新 DNS 条目。它设计用于`cron`工作，但也可以一次性运行。

* * *

```
#!/usr/bin/env python 
import argparse                                                                                                       
import CloudFlare                                          
import logging                                             
import requests                                            

def public_ip(service='http://ip.mrkc.me'):                                                                           
    """                                                    
    Get the apparent public IP of this computer. This does not imply that the
    necessary network configurations are in place to allow public access.

    Raises Exceptions for anything but a 200 response.

    :param service: the url to perform a GET against (should return only an IP)
    :type  service: str
    :returns: this machine's IP as seen by a server on the Internet
    :rtype: str
    """
    logging.debug("Looking up public IP")
    r = requests.get(service)
    r.raise_for_status()
    logging.debug("Found " + r.text)
    return r.text

def update_cf_dns(cf, name, ip_address):
    # 'AAAA' for IPv6, 'A' for IPv4 (default)
    if ':' in ip_address:
        ip_address_type = 'AAAA'
    else:
        ip_address_type = 'A'
    logging.debug("Address type " + ip_address_type)

    # Get the Zone ID
    logging.debug("Looking up zone id for " + name)        
        for zone in cf.zones.get():
            if '.'.join(name.split('.')[-2:]) in zone['name']:
                zone_id = zone['id']
                logging.debug("Found " + zone_id)
                break
        else:
            raise Exception("Unable to find zone id for " + name)

        # Get the DNS record
        logging.debug("Fetching DNS record")
        dns_record = cf.zones.dns_records.get(zone_id,
                params={'name': name, 'match': 'all', 'type': ip_address_type})[0]
        logging.debug("Found " + dns_record['id'])

        # Check to see if the IP has changed
        if dns_record['content'] != ip_address:
            logging.info("Performing DSN record update", dns_record['content'],
                    "->", ip_address)
            cf.zones.dns_records.put(zone_id, dns_record['id'],
                    data={'name': name,
                        'type': ip_address_type,
                        'content': ip_address})
        else:
            logging.debug("Old Address:" + dns_record['content'] +
                    "New Address:" + ip_address)
            logging.info("Not updating")

            def main():
                parser = argparse.ArgumentParser()
                parser.add_argument('--email', type=str, required=True)
                parser.add_argument('--token', type=str, required=True)
                parser.add_argument('--domain', type=str, required=True,
                        help='the domain name to update')
                args = parser.parse_args()
                logging.basicConfig(format='%(asctime)s %(message)s',
                        level=logging.INFO)
                cf = CloudFlare.CloudFlare(email=args.email, token=args.token)
                update_cf_dns(cf, args.domain, public_ip())

            if __name__ == '__main__':
                main() 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用 cron 作业调用它:

```
*/15 * * * *  /root/updatedns.py --email cfemail@domain.tld --token TOKEN --domain something.com 2>&1 | logger -t "updatedns.py" 
```

Enter fullscreen mode Exit fullscreen mode