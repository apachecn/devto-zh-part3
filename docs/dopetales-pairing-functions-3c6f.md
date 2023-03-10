# [双金属]配对函数

> 原文：<https://dev.to/maheshattarde/dopetales-pairing-functions-3c6f>

配对函数对提高应用程序性能非常有用。根据 Wiki，配对函数将两个自然数唯一编码成一个自然数。在设计系统软件时，可以利用这一特性来减少运行时数据结构的数据占用。

这里有一个关于其中一种用途的故事。
在设计编译器诊断或运行时错误检查器时，我们记录来自用户代码的
对的错误位置，此用例的典型实现为

```
typedef std::uint32_t                             Line
typedef std::uint32_t                             Column
typedef std::pair<Line,Column>                    Location;
typedef std::map<Line,Location>               LocationTable; 
// OR
typedef std::multi_map<Line,Location>         LocationTable1; 
```

这个实现产生了错误的语义。地图实施不覆盖同一位置的多个错误。而这两者在语义上都失败了，因为线不能唯一地识别错误及其位置。

所以我们把问题定义为

0<= Line <= 2<sup>32-1</sup>T2 0<=列< = 2 <sup>32-1</sup>

给定< Line,Column >-unique-> Error，我们将需要 O(1)个单键搜索
，比如...

```
typedef std::unordered_map \< LocationHash,ErrorDescriptor \> LocationTable; 
```

所以我们希望将两个 integeres 配对成一个。因此配对函数。
配对函数是一个*可计算的双射*T3】π:N×N→N。

*   行和列都是*自然数*
*   实际上，32 位足以容纳行和数字
*   由两者制成密钥可以容纳在 64 位中

下面是示例实现

```
typedef std::uint32_t  UI32;
typedef std::uint64_t  UI64;
typedef std::uint64_t  Key64;
typedef std::uint32_t  LocationKey;
using LocationHash  = Key64;
typedef std::pair<LocationHash,ErrorDescriptor> LocationListItemPair;

struct PairHashFunction : std::binary_function<UI32,UI32,Key64>
{
    Key64 operator()(UI32 first,UI32 second) const{
        Key64 first64 = (Key64) first;
        Key64 second64 = (Key64) second;
        Key64 sum64 = (first64 + second64);
        Key64 hashValue64 = sum64 * (sum64 + 1) >> 1;
        return hashValue64 + second64;
    }
};

template<typename HashKeyType,typename KeyType>
struct UnPairHashFunctionTemplate
{
    void operator() (HashKeyType hashValue,KeyType *first, KeyType * second){
        HashKeyType temp =  (hashValue << 3)  + 1;
        temp  =  std::sqrt(temp) - 1;
        temp  =  std::floor(temp>>1);
        HashKeyType consum =  (temp * temp  + temp) >> 1;
        *second =  hashValue -  consum;
        *first  =  temp - *second;
    }
};

typedef UnPairHashFunctionTemplate<Key64,UI32> UnPairHashFunction;

class HitTable{
    LocationTable table;
public:
    void hit(Location locKey){
        PairHashFunction hfunc;
        Key64 key = hfunc(locKey.first,locKey.second);
        auto it =  table.find(key);
        if(it == table.end()){
            table[key] = 1;
        }
        else{
            KeyCountType count  = table[key];
            table[key] = count+1;
        }
    }

    bool isHit(Location locKey){
        PairHashFunction hfunc;
        auto it =  table.find(hfunc(locKey.first,locKey.second));
        return (it ==  table.end()) ? false :true;
    }
}; 
```

HTH！