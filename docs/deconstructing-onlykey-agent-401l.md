# 仅解构关键代理

> 原文：<https://dev.to/mpixel/deconstructing-onlykey-agent-401l>

`Log for 2019-06-02:`

今天，我正在开发一个 C#应用程序，它需要通过 SSH 安全地连接到一个远程 Linux 主机。这似乎是一个用我唯一的钥匙的好机会。不幸的是，[only key SSH 代理](https://github.com/trustcrypto/onlykey-agent)的文档留给我的问题多于答案。看起来是时候了...**让我们来看看源代码吧！**

顺便说一下，OnlyKey 是一个小型 USB 设备，大约有闪存盘大小，具有一些与安全相关的功能。在这些功能中，它可以生成和存储数十个密钥，并使用这些密钥进行签名和解密。为了帮助我跟踪事物，它支持给每个键分配一个标签。

注意以`MP`开头的注释是我自己的，通常代替省略的代码。

# 目标

1.  告诉 my OnlyKey 在特定插槽中生成新的 ED25519 密钥。
2.  给钥匙槽贴上适当的标签。
3.  将生成的密钥的公钥检索为 RFC-4716，这样我就可以将它添加到`authorized_keys`中。
4.  使用生成的密钥作为凭证，通过 SSH 与主机交互。

# 文档

首先，我将确定我已经从文档中学到了什么，以及我需要从源代码中确认或发现什么。

## 生成私钥，检索公钥

文件上说，

> 1.  Only the key agent `onlykey-agent user@example.com` is used to generate the public key.
> 2.  Log in to your server as usual and copy the line containing the output from the previous step to your server.
> 
> 上的`~/.ssh/authorized_keys`文件中

我现在知道了为了同时完成目标 1 和目标 3 我需要执行的命令的语法，但是我需要*确认默认行为不会覆盖我在唯一键*上已经有的任何键，并且*发现如果必要的话我如何能够显式地指定一个槽*。

## 给钥匙槽一个标签

文档中没有提到任何关于标签的内容。我需要*确定标签是否是默认创建的*，如果是，那么它是如何确定的。如果没有设置标签，我已经知道如何用一个不同的实用程序来完成，所以我不会在这里深入讨论。

## 与主人互动

文件上说，

> 从现在起，您可以使用以下命令，仅使用一个键登录到您的服务器:`$ onlykey-agent -c user@example.com`

这应该很简单。如果是交互式的，我需要确保我理解如何发送命令和从 C#中检索结果。如果这被证明是困难的，那么*知道是否有办法执行一次性命令*将是有用的。

# 阅读时间！

## 克隆它

和往常一样，我的第一步是将源代码克隆到我的本地机器上，这样我就可以通过适当的 JetBrains IDE(在这里是 [PyCharm](https://www.jetbrains.com/pycharm/) )浏览它，利用我已经内在化的代码导航快捷方式。

```
Set-Location  Code\trustcrypto  git  clone  https://github.com/trustcrypto/onlykey-agent.git 
```

Enter fullscreen mode Exit fullscreen mode

## 插槽选择

我首先要寻找的是默认的插槽选择行为，以及显式指定插槽的能力。

### 输入类型

这是一个命令行实用程序，其中有许多独特的架构，所以我不能立即对代码结构做出任何假设。创建一个键的命令没有使用任何显式的命令，比如我可以搜索的“newkey ”,所以我需要从程序的最开始开始，沿着它的流程，直到我到达第一个参数被解析的地方。作为一个多文件 Python 应用程序，我知道 [`__main__.py`是执行入口点](https://stackoverflow.com/questions/4042905/what-is-main-py)，所以我应该从那里开始。

`__main__.py`包含`import`语句，实例化一个日志记录对象(`log = logging.getLogger(__name__)`，并定义几个函数，但不直接调用其中任何一个。我非常怀疑`getLogger`是为了启动应用程序的参数解析部分，所以“main function”很可能是在一个本地包含中定义的(其主体在导入时是隐式运行的，因为 Python 是一种解释型语言)。事实证明也不是这样！我马上想到了另外两种可能性:

1.  其中一个第三方包含(即`argparse`)通过反射调度执行的流程。
2.  有一个清单为`pip`指定了一个非标准的入口点。

我注意到有一个名为`setup.py`的文件，听起来像是第二种可能性的有力候选者...结果包括以下内容:

```
entry_points={'console_scripts': [
    'onlykey-agent = onlykey_agent.__main__:run_agent',
]} 
```

Enter fullscreen mode Exit fullscreen mode

在那里！我们的入口点是`run_agent`函数(在`__main__.py`中定义)。

### 参数解析

```
@handle_connection_error
def run_agent(client_factory=client.Client):
    """Run ssh-agent using given hardware client factory."""
    args = create_agent_parser().parse_args()
    setup_logging(verbosity=args.verbose)
//MP: Continued farther below after other examples 
```

Enter fullscreen mode Exit fullscreen mode

第一个调用是对`create_agent_parser`的调用——参数解析是高度相关的，所以我将研究这个函数。`create_agent_parser`立即服从`create_parser`；我看到还有一个`create_git_parser`，它也调用`create_parser`，并且所有这些都包含`p.add_argument`调用。由此，我明白我看到了一个继承模式:`create_parser`定义了这个程序所有用法共有的参数，而`create_*_parser`是每个可选的入口点。出于这种探索的目的，我可以将`create_parser`和`create_agent_parser`视为同一个，忽略其他的。

在阅读解析器代码时，我寻找可能存在的任何类型的 slot ID 参数，尽管没有记录下来。

```
def create_parser():
    """Create argparse.ArgumentParser for this tool."""
    p = argparse.ArgumentParser()
    p.add_argument('-v', '--verbose', default=0, action='count')

    curve_names = [name for name in formats.SUPPORTED_CURVES]
    curve_names = ', '.join(sorted(curve_names))
    p.add_argument('-e', '--ecdsa-curve-name', metavar='CURVE',
                   default=formats.CURVE_NIST256,
                   help='specify ECDSA curve name: ' + curve_names)
    p.add_argument('--timeout',
                   default=server.UNIX_SOCKET_TIMEOUT, type=float,
                   help='Timeout for accepting SSH client connections')
    p.add_argument('--debug', default=False, action='store_true',
                   help='Log SSH protocol messages for debugging.')
    return p 
```

Enter fullscreen mode Exit fullscreen mode

`--verbose`、`--timeout`和`--debug`显然都不相关...

```
def create_agent_parser():
    """Specific parser for SSH connection."""
    p = create_parser()

    g = p.add_mutually_exclusive_group()
    g.add_argument('-s', '--shell', default=False, action='store_true',
                   help='run ${SHELL} as subprocess under SSH agent')
//MP: Continued below 
```

Enter fullscreen mode Exit fullscreen mode

然后是`--shell`，它可能与我选择如何将它与 C#集成有关，但听起来它不会与插槽选择有关，所以我稍后将回到它。

```
 //MP: Continued from above def create_agent_parser()
    g.add_argument('-c', '--connect', default=False, action='store_true',
                   help='connect to specified host via SSH')
    p.add_argument('identity', type=str, default=None,
                   help='proto://[user@]host[:port][/path]')
    p.add_argument('command', type=str, nargs='*', metavar='ARGUMENT',
                   help='command to run under the SSH agent')
    return p 
```

Enter fullscreen mode Exit fullscreen mode

接下来是`-c` a.k.a. `--connect`，在文档中已经说得够清楚了。最后是`identity`和`command`，它们似乎都映射到文档中描述的 [*位置*参数](https://www.computerhope.com/jargon/p/positional-parameter.htm)。我没有使用 Python 的`argparse`库的经验，但是看到这种相关性，并且知道“identity”和“command”不是文档中任何示例的一部分，这足以让我得出结论:参数声明前缺少`-`字符会导致它被解释为位置性的。

唉，原来没有任何内置的方法可以通过命令行指定插槽。所以，又回到了`run_agent`:

### 键检索

```
 //MP: Continued from farther above def run_agent(client_factory=client.Client)
    with client_factory(curve=args.ecdsa_curve_name) as conn:
//MP: Continued farther below after other examples 
```

Enter fullscreen mode Exit fullscreen mode

接下来发生的事情是构造和进入`Client`类:

```
class Client(object):
    """Client wrapper for SSH authentication device."""

    def __init__(self, curve=formats.CURVE_NIST256):
        """Connect to hardware device."""
        self.device_name = 'OnlyKey'
        self.ok = OnlyKey()
        self.curve = curve

//MP: Continued below 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么事情。一个`OnlyKey`被实例化了，但是它来自另一个库——我已经知道这不会隐式地创建任何键，因为我以前使用过那个库，但是无论如何这样假设是合理的。`Client`没有定义任何设置器，所以`device_name`和`curve`的存储不会被怀疑是导致密钥生成的执行路径。

```
//MP: Continued from above class Client(object)
    def __enter__(self):
        """Start a session, and test connection."""
        self.ok.read_string(timeout_ms=50)
        empty = 'a'
        while not empty:
            empty = self.ok.read_string(timeout_ms=50)
        return self 
```

Enter fullscreen mode Exit fullscreen mode

`__enter__`实际上是一个无效操作，可以完全删除:`while`的内容将永远不会运行，因为`not 'a'`解析为 false(这些只是*使用*做一些重要事情的代码的残余部分)。

在构造了`OnlyKey`实例之后，`run_agent`“主”函数的下一步是什么？

```
//MP: Continued from farther above def run_agent(client_factory=client.Client)
        label = args.identity
        command = args.command

        public_key = conn.get_public_key(label=label)
//MP: Remaining function body omitted 
```

Enter fullscreen mode Exit fullscreen mode

从参数中检索一些值，将`identity`改名为`label`，然后传递给`get_public_key`(这听起来很有希望)。

```
def get_public_key(self, label):
//MP: Continued below 
```

Enter fullscreen mode Exit fullscreen mode

通过阅读解析器，我们知道在文档的例子中，`label`是“[user@example.com](mailto:user@example.com)”。

```
//MP: Continued from above def get_public_key(self, label)
    # Compute the challenge pin
    h = hashlib.sha256()
    h.update(label)
    data = h.hexdigest()
//MP: Continued below... 
```

Enter fullscreen mode Exit fullscreen mode

这被转换成 256 位的 SHA256 散列，因此 256 位/每字节 8 位= 32 字节长。使用`hexdigest`检索散列，其名称意味着它将返回十六进制编码的散列版本。十六进制编码要求每个字节两个字符，所以我们可以预期这是一个 64 个字符的字符串。

*“计算挑战 pin”*的评论似乎是在转移视线。根据我以前使用 OnlyKey 的经验，这与我见过的其他挑战 pin 码不同，该功能在期待来自设备的有效反馈之前不会向用户显示挑战 pin(正如您将在下面看到的)，并且我不记得曾经必须使用挑战 pin 来检索公钥。这很可能是早期版本的代码中不再相关的遗留物。

```
//MP: Continued from above def get_public_key(self, label)
    if self.curve == formats.CURVE_NIST256:
        data = '02' + data
    else:
        data = '01'+ data

    data = data.decode("hex")
//MP: Continued below 
```

Enter fullscreen mode Exit fullscreen mode

根据曲线类型，在前面加上一个`1`或`2`，然后现在 66 个字符的字符串被解码成一个 33 长的数字数组。

```
//MP: Continued from above def get_public_key(self, label)
    self.ok.send_message(msg=Message.OKGETPUBKEY, slot_id=132, payload=data)
//MP: Continued below... 
```

Enter fullscreen mode Exit fullscreen mode

`OKGETPUBKEY`很明显是一个检索公钥的命令，但剩下的还是有点玄乎:可供选择的槽不到 132 个，除了指定一个槽，发送哈希后的“标签”还有什么意义？

```
//MP: Continued from above def get_public_key(self, label)
    time.sleep(.5)
    for _ in xrange(2):
        ok_pubkey = self.ok.read_bytes(64, to_str=True, timeout_ms=10)
        if len(ok_pubkey) == 64:
            break
//MP: Remaining function body omitted 
```

Enter fullscreen mode Exit fullscreen mode

下一段代码总是期望设备返回一个公钥，因此查询必须足以创建新的密钥并检索现有的密钥。我将不得不查看硬件的固件源代码，以了解设备实际上是如何解释这些消息的！这可以通过跑步来实现...

```
git  clone  https://github.com/trustcrypto/libraries 
```

Enter fullscreen mode Exit fullscreen mode

...并在 CLion 中打开克隆的代码。

### 固件

为了避免这段旅程变得太长，我将总结一下我所知道的位于上面的代理代码和下面的固件代码之间的`onlykey` Python 库:发送的消息是下面的一系列字节:`{ /* header */ 255, 255, 255, 255, /* message */ OKGETPUBKEY, /* slot */ 132, /* payload */ ... }`。固件中的有效入口点是来自`okcore.cpp`的`void recvmsg()`，而`OKGETPUBKEY`消息由`switch (recv_buffer[4])`的`case OKGETPUBKEY:`拾取。这又调用了`okcrypto.cpp` :
中定义的`void GETPUBKEY (uint8_t *buffer)`

```
uint8_t temp[64] = {0};
#ifdef DEBUG Serial.println();
Serial.println("OKGETPUBKEY MESSAGE RECEIVED"); 
#endif if (buffer[5] < 5 && !outputU2F && !buffer[6]) { //Slot 101-132 are for ECC, 1-4 are for RSA
    if (onlykey_flashget_RSA ((int)buffer[5])) GETRSAPUBKEY(buffer);
} else if (buffer[5] < 131 && !outputU2F && !buffer[6]) { //132 and 131 are reserved 
    if (onlykey_flashget_ECC ((int)buffer[5])) GETECCPUBKEY(buffer);    
} else if (buffer[6] <= 3 && !outputU2F) { // Generate key using provided data, return public
    DERIVEKEY(buffer[6], buffer+7);
    RawHID.send(ecc_public_key, 0);
} else if (buffer[6] == 0xff) { //Search Keylabels for matching key, return slot
    //MP: omitted, not relevant
} 
```

Enter fullscreen mode Exit fullscreen mode

马上，评论里已经有了一些答案:*“槽 101-132 为 ECC，1-4 为 RSA132 和 131 是保留的"*。本例中的`buffer[5]`是`132`，因此前两个`if`语句解析为 false。第三个测试只测试`buffer[6]`——这是有效载荷的第一个字节，我知道根据曲线类型，它可以是 1 或 2。这两个可能的值都是`<= 3`，我认为假设`outputU2F`为假是安全的，因为我们没有做任何与 U2F 相关的事情，所以肯定是这个分支被采用了。描述为*“使用提供的数据生成密钥，返回 public”*。

这正在变成一个迷人的谜！这些特殊的 ECC 插槽 31 和 32 的行为到底是什么？似乎它们总是生成，而不是简单地恢复。而`buffer[6]`是`3`又意味着什么？散列身份扮演什么角色？？

用曲线类型和身份散列调用`DERIVEKEY`，预计这将导致全局变量`ecc_public_key`被填充公钥，并通过 USB HID 发送回程序(返回 Python)。在这一点上可以得出一个有趣的结论:`!buffer[6]`(曲线类型)在前两个 if 语句中被测试，而`buffer[5]`(槽 ID)在第三个语句中根本没有被使用，这意味着**当一个曲线类型被定义时，槽 ID 实际上并不重要！在我看来，就二进制 API 而言，这有点草率。毫无疑问，这是一种误导:Python 代码中不必要的插槽号让我认为它和其他插槽没有什么不同。完全省略插槽号，或者使用`0xFF`作为插槽号，会更清楚地传达“这不是你放入插槽的普通钥匙，它是特殊的和固定的”。如果不管指定哪个插槽，都得到相同的结果，这也会让一些程序员感到困惑。**

无论如何，下一步是探索`DERIVEKEY`到底在做什么，以便对这里到底发生了什么有一个满意的理解。这是一个相对简洁和复杂的 C 语言，所以与上面的所有代码不同，我提供了我自己版本的原始代码，它简化了事情并使用了更多的交流变量名，但应该完全一样:

```
#define flashstorestart 0x3B000
#define flashstoresec7  (flashstorestart + 12288ul)
#define EEpos_ecckey1   (EEpos_totpkey24len + EElen_totpkeylen)
#define EElen_ecckey    1 
void DERIVEKEY (uint8_t ktype, uint8_t *data)
{
    onlykey_flashget_ECC (132);
//MP: This function continued much farther below
}

int onlykey_flashget_ECC (uint8_t slot)
{
    uint8_t slotIndex = slot - 101;
    extern uint8_t typeAndFeatures;
    onlykey_eeget_common(/* out */ &typeAndFeatures, EEpos_ecckey1 + slotIndex * EElen_ecckey, EElen_ecckey);
    uint8_t type = typeAndFeatures & 0x0F;
//MP: This function continued below
}

int onlykey_eeget_common (uint8_t *ptr, int addr, int len) {
    while (len--) { *ptr++ = eeprom_read_byte(addr++); }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   现在很清楚了，按照惯例，ECC 密钥数偏移 100，这解释了为什么我们要求 132，而它们只上升到 32。
*   `EEpos_ecckey1`或“EEPROM 中第一个 ECC 密钥的位置”被定义为紧接在第 24 个 [TOTP](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm) 密钥之后，这又可能被定义为在其他东西之后，最终从存储器的开始导出。这是定义内存位置的一种便捷方式——如果固件作者想要重新安排、添加或删除 EEPROM 结构中的内容，只需在修改后立即更改一个定义。
*   `EElen_ecckey`或“EEPROM 中 ECC 密钥的长度”只有一个字节。因此，存储在那里的显然不是 ECC 密钥本身，而是该密钥的某个一字节属性。
*   `onlykey_eeget_common`将一个字节从 EEPROM 复制到变量`type`(它是一个外部可访问的堆变量的事实似乎还没有相关性——也许这些限定符是`eeprom_read_byte`返回的任何内存的要求)。

由此，我可以断定首先发生的是它读取密钥的*曲线类型*和*特征*(可能指签名/解密/备份)。发生的第二件事...

```
 extern uint8_t ecc_public_key[(MAX_ECC_KEY_SIZE*2)+1];
extern uint8_t ecc_private_key[MAX_ECC_KEY_SIZE];
extern uint8_t profilekey[32];

//MP: Continued from above int onlykey_flashget_ECC (uint8_t slot)
    unsigned long ptrToKeyInFlash = flashstoresec7 + slotIndex * 32;
    onlykey_flashget_common(ecc_private_key, ptrToKeyInFlash, 32);
//MP: This function continued below
}

void onlykey_flashget_common (uint8_t *dest, unsigned long *src, int lenBytes) {
    for (int z = 0; z < lenBytes; z += 4)
    {
        *(ptr++) = (uint8_t) ((*src >> 24) & 0xFF);
        *(ptr++) = (uint8_t) ((*src >> 16) & 0xFF);
        *(ptr++) = (uint8_t) ((*src >> 8)  & 0xFF);
        *(ptr++) = (uint8_t) ((*(src++))   & 0xFF);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

...密钥本身从闪存中读入一个全局变量`ecc_private_key`，类似于我在`void GETPUBKEY (uintu_t* buffer)`中看到的全局变量`ecc_public_key`:`onlykey_flashget_common`从给定的“from address”中读取四次，然后递增，每次移位的位数递减。这似乎是一个[大端/小端](https://en.wikipedia.org/wiki/Endianness)转换。我想我可以把它留在“flash_get 听起来像是从闪存中复制东西”，但观察到 flash 和 RAM 使用统一的内存地址空间，但字节序不同，这很有趣。

至于剩下的代码...

```
//MP: Continued from above int onlykey_flashget_ECC (uint8_t slot)
    aes_gcm_decrypt(ecc_private_key, slot, features, profilekey, 32);
    if (type==1)
        Ed25519::derivePublicKey(ecc_public_key, ecc_private_key);
    else if (type==2) {
        const struct uECC_Curve_t * curve = uECC_secp256r1();
        uECC_compute_public_key(ecc_private_key, ecc_public_key, curve);
    }
    else if (type==3) {
        const struct uECC_Curve_t * curve = uECC_secp256k1();
        uECC_compute_public_key(ecc_private_key, ecc_public_key, curve);
    }
    return typeAndFeatures;
} 
```

Enter fullscreen mode Exit fullscreen mode

...这些函数的实现越来越复杂和低级，所以这次我实际上坚持根据它们的名称来猜测:

*   私钥在闪存中加密，因此需要在易失性 RAM 中解密。
*   公钥是从私钥派生出来的——根据指定的密钥类型，使用不同的算法。

### 装神弄鬼的钥匙#32

在这一点上，有一个有趣的矛盾:一个键是从内存中读取的——还没有从零开始创建任何东西。关键点的曲线类型存储在只读存储器中(可以通过将硬件置于特殊模式来改变，但我们不在该模式下，所以它来自某个先前的时间点)。这意味着，无论我们为代理指定什么曲线类型，也无论哪个槽，它都会读取 ECC 槽 32 中的任何键，无论该键使用什么曲线。那么什么是关键呢？它从哪里来？

基于主函数名的模式，我搜索了`SETPRIV`，注意到它在几个地方被使用。我第一个跳出来...

```
// Generate and encrypt default key
recv_buffer[4] = 0xEF; //MP: OKSETPRIV
recv_buffer[5] = 0x84; //MP: 132
recv_buffer[6] = 0x61; //MP: binary 0110 0001
RNG2(recv_buffer+7, 32);
SETPRIV(recv_buffer); //set default ECC key 
```

Enter fullscreen mode Exit fullscreen mode

...似乎在方法`onlykey_flashset_pinhashpublic`中设置了密钥，我可以从仅从`SETPIN`调用该方法这一事实来推测，该方法在设置设备时被调用一次(唯一设置 PIN 的点)。

按照代码，`SETPRIV`几乎是`GETPUBKEY`的镜像，`onlykey_eeset_ecckey`几乎是`onlykey_eeget_ecckey`的镜像，以此类推。按照这些思路，我确认了`buffer[6]` ( `0x61`)被存储到 EEPROM 中，与`onlykey_flashget_ECC`检索`typeAndFeatures`的地址相同。从该函数的`uint8_t type = typeAndFeatures & 0x0F;`中，我知道只有较小的半字节表示类型——在本例中，1 表示 ED25519。**总之，当设备初始化时，插槽 32 被赋予一个在设备上生成的 ED25519 私钥。**

### 古怪的密码

然后是有效载荷:`RNG2`，包含像`rngloop(); //Gather entropy`这样有趣的行，似乎是 OnlyKey 自豪地吹捧的随机数生成的实现，它使用传感器来考虑环境熵。因此...是垃圾数据，还是实际上是私钥？快速搜索“ED25519 随机数”，我找到了答案，[“是的”](https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key)。

这个答案，特别是来自用户 [Maarten Bodewes](https://crypto.stackexchange.com/users/1172/maarten-bodewes) 的评论*“注意，这不是特定于 ed 25519 curve”*，开始揭示看似荒谬背后的实际意义:密钥曲线选项之间的差异仅在公钥中表现出来。又搜索了几次，我找到了[的另一个帖子](https://crypto.stackexchange.com/questions/54660/is-it-safe-to-use-same-private-key-in-two-or-more-ec-signature-algorithms)，它进一步证实了这一点，同样是这个马腾也在上面发表了评论！关于第二个话题，他补充道，*“你也可以存储 128 位或更多的数据，并使用带有两个标签的 KDF 从中获得两个密钥。”*。我不知道什么是 KDF，但是使用“标签”来“派生”更多的密钥听起来非常像我在检索公钥#32 的代码中看到的那样。让我们重温一下代码的其余部分。

```
//MP: Continued from farther above void DERIVEKEY (uint8_t ktype, uint8_t *data)
//MP: Remember, in this context, `data` is the identity hash
    memset(ecc_public_key, 0, sizeof(ecc_public_key));
    SHA256_CTX ekey;
    sha256_init(&ekey);
    sha256_update(&ekey, ecc_private_key, 32); //Add default key to ekey 
    sha256_update(&ekey, data, 32); //Add provided data to ekey
    sha256_final(&ekey, ecc_private_key); //Create hash and store
//MP: Continued below
} 
```

Enter fullscreen mode Exit fullscreen mode

私钥与身份散列一起被[加盐](https://crypto.stackexchange.com/questions/1776/can-you-help-me-understand-what-a-cryptographic-salt-is)，以便创建一个新的私钥(我现在知道随机数对于这些算法是有效的私钥，所以 sha256 结果也足够了)。注释中的“ekey”可能意味着“临时密钥”。这种加盐(特别是，曲线类型包含在身份散列中的事实)可能会阻止[耶胡达·林德尔](https://crypto.stackexchange.com/users/25354/yehuda-lindell)在[的第二个堆栈交换链接](https://crypto.stackexchange.com/questions/54660/is-it-safe-to-use-same-private-key-in-two-or-more-ec-signature-algorithms)中提到的理论攻击:

> ...在不同的曲线上有两个公钥和同一个私钥可能会泄露信息。我不知道如何，但我确信你不能证明从一个减少到另一个...

换句话说，盐和哈希似乎是 KDF 的 T1。

```
//MP: Continued from above void DERIVEKEY (uint8_t ktype, uint8_t *data)
    if (ktype==1) {
        Ed25519::derivePublicKey(ecc_public_key, ecc_private_key);
        return;
    }
    else if (ktype==2) {
        const struct uECC_Curve_t * curve = uECC_secp256r1();
        uECC_compute_public_key(ecc_private_key, ecc_public_key, curve);
    }
    else if (ktype==3) {
        const struct uECC_Curve_t * curve = uECC_secp256k1();
        uECC_compute_public_key(ecc_private_key, ecc_public_key, curve);
    } 
```

Enter fullscreen mode Exit fullscreen mode

最后，指定的曲线用于创建公钥。

第三种未记录的密钥类型(secp256*k*1)受设备固件支持，尽管代理库尚不支持它。也许它不常用。

### 为插槽选择的结论

**插槽选择完全没有必要。**我们有一个非常巧妙的设置，设备上的一个特殊私钥可以生成无限量的短暂但确定的密钥，因此每个用户/主机组合都会生成一个唯一的密钥。

为什么文档没有包括太多的细节现在更有意义了，但是我在这里的旅程说明了除了什么在起作用之外，文档如何需要描述什么没有在起作用。哪里有突出的简单性，哪里就必须突出这种简单性，否则用户将会用他们从其他软件那里学到的假设来填补空白，因为他们从经验中知道大多数软件文档*没有*排除重要的细节。

## 标签

我在这段代码中还能找到什么呢？我已经忘记了这一点...

没错。标签。如果插槽不相关，那么标签也不相关。继续前进！

## 交互性

还记得解析器中未记录的“shell”参数吗？它在法典中被描述为，

> 在 SSH 代理下将${SHELL}作为子进程运行

起初，这让我很困惑。shell 将*而不是*作为子进程运行意味着什么？但是后来我从文档中想起了这个:

> 这个方法也可以用于 git push 或者其他使用 SSH 作为通信协议的机制:
> `$ onlykey-agent user@example.com git push`

知道了这一点，我怀疑`--shell`是一种进入交互式 shell 的方式，在这里您可以使用“正在使用 SSH 的机制”,并期望代理处理上述 SSH 使用的授权。注意到`g = p.add_mutually_exclusive_group()`的存在支持了这种怀疑，表明`--shell`是一个与`--connect`互斥的动作。但是现在，我打算直接在远程 shell 中运行命令。

所以，关于这个主题剩下的就是找出将 C#与交互式进程挂钩的最佳实践，这不涉及阅读源代码。因此...

# 全部搞定！

总之，*插槽选择完全没有必要，因此*的标注也是如此。我只需要运行`onlykey-agent -e ed25519 user@host`来获取公钥，运行`onlykey-agent -e ed25519 -c user@host`来启动交互式 SSH 会话。

那么所有这些都是白费力气吗？

一点也不！

我学到了一些关于密码学的非常有趣的新东西，并且一路上都很开心。有时，阅读源代码会得出这样的结论:您应该一直按照文档中所说的去做。但是在那些情况下，这仍然是值得做的，因为你最终会更好地理解*为什么*。

我相信我需要做一些实际上完全没有必要的事情，这是软件工程中常见的错误。这个错误源于这样一个事实，即我所学会的思考由 SSH 密钥解决的问题的方式不同于 trustcrypto 决定用`onlykey-agent`解决这个问题的方式。不是典型的范例*让我们随机生成一个密钥作为我的身份*，而是*让我们确定性地生成一个基于我的身份*的密钥。请记住，在编写您自己的文档时，除了语法之外，描述范例和动机也可以以这种方式对您的用户有用。

# F U T U R E

我经常阅读源代码，无论是在工作中还是在空闲时间，并认为就后者发布这样的日志会很好。我的愿望是，这是许多未来的第一个。如果你有任何批评或建议，请让我知道！

如果您需要帮助记录或逆向工程您公司使用的任何开源或遗留代码，请通过 [Freeform Labs](https://www.freeformlabs.xyz) 联系我。