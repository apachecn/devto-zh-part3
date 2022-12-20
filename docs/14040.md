# 🍗显示 Dev: json-schema-mapper - JSON 模式转换为 jsonSerializable PHP 类

> 原文：<https://dev.to/howyi/show-dev-json-schema-mapper---json-schema-convert-to-jsonserializable-php-classes--j24>

⭐

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [豪伊](https://github.com/howyi) / [ json-schema-mapper](https://github.com/howyi/json-schema-mapper)

### JSON 模式对象| PHP 类的枚举

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/96dd3962be03f31546f75b4162cfa74e.png) ](https://travis-ci.org/howyi/json-schema-mapper) [ ![Coverage Status](img/193c32d2f90fc8ce77fd99046e69132e.png)](https://coveralls.io/github/howyi/json-schema-mapper?branch=master)

# json 模式映射器

JSON 模式对象| PHP 类的枚举

文件:[https://howyi.gitbooks.io/json-schema-mapper/](https://howyi.gitbooks.io/json-schema-mapper/)

```
composer require howyi/json-schema-mapper
```

```
./vendor/bin/jsm map [jsonDir] [phpDir] [namespace] [templatePath]
```

</article>

[View on GitHub](https://github.com/howyi/json-schema-mapper)

# 动机

我厌倦了制作一个匹配 Json-schema 的 PHP 类😵

# 安装

```
$ composer require howyi/json-schema-mapper 
```

# 用法

指定目标 JSON 目录和 map (PHP)目录和名称空间，并执行

```
$ ./vendor/bin/jsm map [jsonDir] [phpDir] [namespace] [templatePath] 
```

# 描述

一个生成 PHP 类的库，可以用`\json_encode()`从 JSON Schema 中获取

尽可能将 JSON 模式类型转换成 PHP 类型，并做类型提示

当“类型为字符串，格式为日期时间”时，类型提示变为`\ DateTimeInterface`，转换数组时自动执行`->format(\ DateTime::RFC3339)`

$ ref 指定的对象被转换为映射接口的类型提示

枚举作为继承`Eloquent\AbstractEnumration`的枚举类单独生成

# 举例

## 目录

JSON dir

```
sample/json/
  ├ neta.json
  ├ osakanaType.json
  ├ shari.json
  └ sushi.json 
```

转换的 PHP 目录

```
sample/generated/
  ├ Neta/
  │  ├ Neta.php
  │  ├ NetaInterface.php
  │  └ NetaTrait.php
  ├ Shari/
  │  ├ Shari.php
  │  ├ ShariInterface.php
  │  └ ShariTrait.php
  ├ Sushi/
  │  ├ Sushi.php
  │  ├ SushiInterface.php
  │  └ SushiTrait.php
  └ OsakanaType.php 
```

## 文件

-伊甸园字幕组=-翻译:粒粒粒粒尘紫月皮皮夏酷校对:葱家的小毛毛时间轴:邦德猪

```
{  "title":  "寿司情報",  "type":  "object",  "properties":  {  "sushiId":  {  "title":  "寿司 ID",  "type":  "integer"  },  "sushiName":  {  "title":  "寿司の名前",  "type":  "string"  },  "eatable":  {  "title":  "食べられるか",  "type":  "boolean"  },  "length":  {  "title":  "寿司の長さ",  "type":  "number"  },  "osakanaType":  {  "title":  "お魚タイプ",  "$ref":  "osakanaType.json"  },  "neta":  {  "title":  "ネタ",  "$ref":  "./neta.json"  },  "shari":  {  "title":  "シャリ",  "$ref":  "./shari.json"  },  "expirationDate":  {  "title":  "消費期限",  "type":  "string",  "format":  "date-time"  }  },  "required":  [  "sushiId",  "sushiName",  "eatable",  "osakanaType",  "neta",  "shari",  "expirationDate"  ],  "additionalProperties":  true  } 
```

转换后的 PHP(类)

```
<?php

namespace Json\Sushi;

use Json\OsakanaType;
use Json\Neta\NetaInterface;
use Json\Shari\ShariInterface;

class Sushi implements SushiInterface
{
    use SushiTrait;

    protected $sushiId;
    protected $sushiName;
    protected $eatable;
    protected $length;
    protected $osakanaType;
    protected $neta;
    protected $shari;
    protected $expirationDate;
    protected $additionalProperties;

    public function __construct(
        int $sushiId,
        string $sushiName,
        bool $eatable,
        ?float $length,
        OsakanaType $osakanaType,
        NetaInterface $neta,
        ShariInterface $shari,
        \DateTimeInterface $expirationDate,
        array $additionalProperties = []
    ) {
        $this->sushiId = $sushiId;
        $this->sushiName = $sushiName;
        $this->eatable = $eatable;
        $this->length = $length;
        $this->osakanaType = $osakanaType;
        $this->neta = $neta;
        $this->shari = $shari;
        $this->expirationDate = $expirationDate;
        $this->additionalProperties = $additionalProperties;
    }

    public function sushiId(): int
    {
        return $this->sushiId;
    }

    public function sushiName(): string
    {
        return $this->sushiName;
    }

    public function eatable(): bool
    {
        return $this->eatable;
    }

    public function length(): ?float
    {
        return $this->length;
    }

    public function osakanaType(): OsakanaType
    {
        return $this->osakanaType;
    }

    public function neta(): NetaInterface
    {
        return $this->neta;
    }

    public function shari(): ShariInterface
    {
        return $this->shari;
    }

    public function expirationDate(): \DateTimeInterface
    {
        return $this->expirationDate;
    }

    public function additionalProperties(): array
    {
        return $this->additionalProperties;
    }
} 
```

转换后的 PHP(Trait)

```
<?php

namespace Json\Sushi;

trait SushiTrait
{
    use \JsonSchemaMapper\ObjectTrait;

    public function jsonProperties(): array
    {
        return [
            'sushiId',
            'sushiName',
            'eatable',
            'length',
            'osakanaType',
            'neta',
            'shari',
            'expirationDate',
        ];
    }

    public function allowAdditionalProperties(): bool
    {
        return true;
    }
} 
```

转换后的 PHP(接口)

```
<?php

namespace Json\Sushi;

use JsonSchemaMapper\JsonArrayAccess;
use Json\Neta\NetaInterface;
use Json\OsakanaType;
use Json\Shari\ShariInterface;

interface SushiInterface extends JsonArrayAccess, \JsonSerializable
{
    public function toJsonArray(): array;

    public function jsonProperties(): array;

    public function allowAdditionalProperties(): bool;

    public function sushiId(): int;

    public function sushiName(): string;

    public function eatable(): bool;

    public function length(): ?float;

    public function osakanaType(): OsakanaType;

    public function neta(): NetaInterface;

    public function shari(): ShariInterface;

    public function expirationDate(): \DateTimeInterface;

    public function additionalProperties(): array;
} 
```