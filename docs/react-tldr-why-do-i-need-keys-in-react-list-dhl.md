# React tldr 为什么我需要 React 列表中的键？

> 原文：<https://dev.to/maniekm/react-tldr-why-do-i-need-keys-in-react-list-dhl>

列表很重要，需要小心使用。

确保列表中的每一项都有唯一的键。

最好不要使用索引作为键，除非你确定列表是一个静态列表(没有添加/重新排序/删除列表)。

切勿使用 Math.random()等不稳定的密钥来生成密钥。

如果使用不稳定的键，React 将出现性能下降和意外行为。

来源:[https://medium . com/@ adhithiravi/why-do-I-need-keys-in-react-lists-dbb 522188 BBB？FB clid = iwar 09 xvioqdhr c 7 gvvolxxdgvfvbtxbkcylq 0 p 2 EUR-JKR HW 9 eumipz 7 c 0 bq](https://medium.com/@adhithiravi/why-do-i-need-keys-in-react-lists-dbb522188bbb?fbclid=IwAR09xviOqDHRc7gvvoLXXDgVfVBtxbkCylq0p2Eur-jKrhw9EuMIpZ7c0BQ)