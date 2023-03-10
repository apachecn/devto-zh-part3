# 代码。改变它而不破坏它，用一个装饰器。

> 原文：<https://dev.to/kchawla_pi/using-a-decorators-to-solve-my-task-the-thinking--the-process-49f0>

一个人如何从知道功能到使用它来解决问题？

答案是把它用在一个个人影响不大的副业项目上。阅读这样的帖子可能也会有所帮助。

我最近开始作为一名 python 开发人员为 Nilearn([http://nilearn.github.io/](http://nilearn.github.io/))&Nistats([http://nistats.github.io/](http://nistats.github.io/))工作，这两个开源 Python 库用于神经科学中分析功能性 MRI 数据。

在 Nilearn 中合并了最近的一个 pull-request 之后，我意识到，对于像我这样刚接触开发工作并且在之前的职业中没有编写过商业代码的人来说，记录整个过程是很有用的。

我决定追溯和重建我的步骤和我当时的想法，添加代码和文本来说明它们。这有一个不幸的副作用，即细节的深度只和我的记忆一样好，实际的代码样本只和我提交的代码一样好。还会有一些我写的代码示例，我从来没有提交过，或者从来没有写过，只是在我的脑海中认为是可能的。

这不应该降低这篇文章的效用。

请注意，我(最终)使用了*装饰者*和`**kwargs`，但没有解释这些，因为这不是我写这篇文章的目的，网上有很多教程可以理解这两个想法。

此外，这些库目前与 Python2 兼容，所以我们的选择有时会稍微受到限制。

也就是说，Geronimo！

# 开场白

这是我们图书馆的功能。为了简洁起见，我删除了 docstring，该功能对于本文并不重要:

```
def view_connectome(adjacency_matrix, 
                    coords, 
                    threshold=None,
                    cmap=cm.cyan_orange, 
                    symmetric_cmap=True,
                    linewidth=6., 
                    marker_size=3.,
                    ):
    connectome_info = _get_connectome(
        adjacency_matrix, coords, threshold=threshold, cmap=cmap,
        symmetric_cmap=symmetric_cmap)
    connectome_info["line_width"] = linewidth
    connectome_info["marker_size"] = marker_size
    return _make_connectome_html(connectome_info) 
```

任务是使`view_connectome()`与库中另一个更老的函数`plot_connectome()`一致。我们希望它们有相同的函数签名。

为什么？一致性，如果处理得当和合理，可以有助于易用性。本质上，一旦用户理解了我们的<> _connectome()函数之一，他们就会知道如何调用所有这些函数。

我们想更改函数签名中某些参数的名称:

坐标>节点 _ 坐标
cmap >边缘 _cmap
阈值>边缘 _ 阈值
标记 _ 大小>节点 _ 大小

# 第零次迭代:

如果我是这个库的唯一用户，那么更改非常简单:

```
def view_connectome(adjacency_matrix, 
                    node_coords,  # change param name here,
                    edge_threshold=None,  # here,
                    edge_cmap=cm.cyan_orange, # here,
                    symmetric_cmap=True,
                    linewidth=6., 
                    node_size=3.,  # and here.
                    ):
    connectome_info = _get_connectome(
    # rename the variables in this line, ...
        adjacency_matrix, node_coords, threshold=edge_threshold, cmap=edge_cmap,
        symmetric_cmap=symmetric_cmap)
    connectome_info["line_width"] = linewidth
    connectome_info["marker_size"] = node_size  # ...and here.
    return _make_connectome_html(connectome_info) 
```

重命名函数签名中的参数，重命名函数体内的变量，对文档字符串 aand...
轰！搞定了。

不是。

考虑因素:

我不是这个库的唯一用户。如果我以这种方式进行更改，如果其他用户使用关键字参数(他们应该这样做)，就会破坏他们现有的代码。

请注意，我没有到处更改名称，只是在参数和相关变量中。我没有在`connectome_info["marker_size"] = node_size`中更改键名，也没有更改`_get_connectome()`中的`cmap`参数。

这是因为:

1.  这不是 PR 的初衷，PR 应该尽可能的简洁，以便于查看、跟踪、调试和检查变更。任何额外的问题或潜在的改进或代码清理都是一个新的问题，一个新的 PR，可以同时提交，并与当前的 PR 分开。
2.  改变其他任何事情都可能引发下游问题，需要加以注意。
3.  在 Python 中,`_get_connectome()`是一个私有函数，而不是面向用户的函数，所以从对用户界面的影响来看，对它进行修改要简单得多。

我们需要做的是找到一种方法:

1.  确保位置参数不中断。
2.  让参数的新名称和旧名称都工作一段时间。
3.  在此过渡阶段向用户显示弃用警告。

对于 Nilearn 来说，这个过渡期通常意味着 2 个版本(大约 8-11 个月)，包括点/次要版本。

上述实现仅满足第一个考虑(1。确保位置参数不中断。).

# 第一次迭代

这很容易做到。我们用新的参数替换原来的参数，以保留位置参数，然后在最后添加替换的参数，这样旧的带关键字的参数仍然有效。

然后，我们在这个函数体中添加代码，将 args 传递给新参数，并生成适当的弃用警告。

```
import warnings

def view_connectome(adjacency_matrix, 
                    node_coords, 
                    edge_threshold=None,
                    edge_cmap=cm.cyan_orange, 
                    symmetric_cmap=True,
                    linewidth=6., 
                    node_size=3.,
                    # placing old params here to preserve old keyworded args.
                    coords=None, threshold=None, cmap=None, marker_size=None,
                    ):
    # code for the deprecation
    param_deprecation_msg_template = ('The param {} is being deprecated and will be  replaced by the param {} in future. Please use {}.')
    if marker_size:
        node_size = marker_size
        warnings.warn(param_deprecation_msg_template.format('marker_size',
                                                            'node_size',
                                                            'node_size',
                                                            )
    if threshold is not None:  # numpy arrays don't have unambiguous truthiness.
        edge_threshold = threshold
        warnings.warn(param_deprecation_msg_template.format('threshold',
                                                            'edge_threshold',
                                                            'edge_threshold',
                                                            )
    if cmap is not None:  # numpy arrays don't have unambiguous truthiness.
        edge_cmap = cmap
        warnings.warn(param_deprecation_msg_template.format('cmap',
                                                            'edge_cmap',
                                                            'edge_cmap',
                                                            )
    if coords is not None:  # numpy arrays don't have unambiguous truthiness.
        node_coords = coords
        warnings.warn(param_deprecation_msg_template.format('coords',
                                                            'node_coords',
                                                            'node_coords',
                                                            )    
    # original functionality of the function,
    connectome_info = _get_connectome(adjacency_matrix, 
                                      node_coords,
                                      edge_threshold=threshold,
                                      cmap=edge_cmap,
                                      symmetric_cmap=symmetric_cmap,
                                      )
    connectome_info["line_width"] = linewidth
    connectome_info["marker_size"] = node_size
    return _make_connectome_html(connectome_info) 
```

好吧，那行，但是(胡)人！那看起来不干净吗？！我让代码变得更长、更难阅读、看起来不舒服，而且函数体现在做了不止一件事:解析和处理参数以及它最初在做什么。

为了简洁起见，我特意省略了这里的`warnings.filter()`部分(默认情况下`DeprecationWarnings`不向最终用户显示)。

# 第二次迭代

我决定将 verbose warnings 部分重构为它自己的私有函数，并从主体函数中调用它。

```
 def view_connectome(adjacency_matrix, 
                    node_coords, 
                    edge_threshold=None,
                    edge_cmap=cm.cyan_orange, 
                    symmetric_cmap=True,
                    linewidth=6., 
                    node_size=3.,
                    **kwargs,
                    ):
    # generate the deprecation warnings
    kwargs = _param_deprecation_view_connectome(kwargs)

    # handover the args from old params to new ones.
    if kwargs['marker_size']:
        node_size = marker_size
    if kwargs['threshold'] is not None:
        edge_threshold = threshold
    if kwargs['cmap'] is not None:
        edge_cmap = cmap
    if kwargs['coords'] is not None:
        node_coords = coords

    connectome_info = _get_connectome(
        adjacency_matrix, node_coords, threshold=edge_threshold, cmap=edge_cmap,
        symmetric_cmap=symmetric_cmap)
    connectome_info["line_width"] = linewidth
    connectome_info["marker_size"] = node_size
    return _make_connectome_html(connectome_info)

def _param_deprecation_view_connectome(kwargs):
    kwargs.setdefault('marker_size', None)
    kwargs.setdefault('threshold', None)
    kwargs.setdefault('cmap', None)
    kwargs.setdefault('coords', None)

    param_deprecation_msg_template = ('The param {} is being deprecated and will be  replaced by the param {} in future. Please  use {}.')
    if kwargs['marker_size']:
        warnings.warn(param_deprecation_msg_template.format('marker_size',
                                                             'node_size',
                                                             'node_size',
                                                            )
    if kwargs['threshold'] is not None:  # numpy arrays don't have unambiguous truthiness.
        warnings.warn(param_deprecation_msg_template.format('threshold',
                                                            'edge_threshold',
                                                            'edge_threshold',
                                                            )
    if kwargs['cmap'] is not None:  # numpy arrays don't have unambiguous truthiness.
        warnings.warn(param_deprecation_msg_template.format('cmap',
                                                            'edge_cmap',
                                                            'edge_cmap',
                                                            )
    if kwargs['coords'] is not None:  # numpy arrays don't have unambiguous truthiness.
        warnings.warn(param_deprecation_msg_template.format('coords',
                                                            'node_coords',
                                                            'node_coords',
                                                           )    
    return kwargs 
```

好多了。我是这样想的:

1.  添加到原始函数中的代码行数更少。
2.  更容易编写单元测试来检查引发的警告。
3.  函数签名几乎没有改变超过最小的必要性。

和...

1.  然而，必须在原始函数中添加几行代码。
2.  没有单元测试来测试旧参数值到新参数值的正确转换。
3.  函数签名的更改超出了最低要求。

# 第三次迭代

因为本质上我想做的是修改一个现有函数的行为，或者*修饰*它，我决定使用一个专门用于这个任务的 Python 特性 Decorators。

所以我写了一个。

```
 def _deprecate_params_view_connectome(func):
    """ Decorator to deprecate specific parameters in view_connectome()
     without modifying view_connectome().
     """
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        _warn_deprecated_params_view_connectome(kwargs)
        kwargs = _transfer_deprecated_param_vals_view_connectome(kwargs)
        return func(*args, **kwargs)

    return wrapper

@\_deprecate_params_view\_connectome
def view_connectome(adjacency_matrix, node_coords, edge_threshold=None,
                    edge_cmap=cm.bwr, symmetric_cmap=True,
                    linewidth=6., node_size=3.,
                    **kwargs):
    connectome_info = _get_connectome(
        adjacency_matrix, node_coords, threshold=edge_threshold, cmap=edge_cmap,
        symmetric_cmap=symmetric_cmap)
    connectome_info["line_width"] = linewidth
    connectome_info["marker_size"] = node_size
    return _make_connectome_html(connectome_info)

def _warn_deprecated_params_view_connectome(kwargs):
    """ For view_connectome(), raises warnings about deprecated parameters.
    """
    all_deprecated_params = {'coords': 'node_coords',
                             'threshold': 'edge_threshold',
                             'cmap': 'edge_cmap',
                             'marker_size': 'node_size',
                             }
    used_deprecated_params = set(kwargs).intersection(all_deprecated_params)
    for deprecated_param_ in used_deprecated_params:
        replacement_param = all_deprecated_params[deprecated_param_]
        param_deprecation_msg = (
            'The parameter "{}" will be removed in Nilearn version 0.6.0\. '
            'Please use the parameter "{}" instead.'.format(deprecated_param_,
                                                            replacement_param,
                                                            )
        )
        warnings.filterwarnings('always', message=param_deprecation_msg)
        warnings.warn(category=DeprecationWarning,
                  message=param_deprecation_msg,
                  stacklevel=3)

def _transfer_deprecated_param_vals_view_connectome(kwargs):
    """ For view_connectome(), reassigns new parameters the values passed
    to their corresponding deprecated parameters.
    """
    coords = kwargs.get('coords', None)
    threshold = kwargs.get('threshold', None)
    cmap = kwargs.get('cmap', None)
    marker_size = kwargs.get('marker_size', None)

    if coords is not None:
        kwargs['node_coords'] = coords
    if threshold:
        kwargs['edge_threshold'] = threshold
    if cmap:
        kwargs['edge_cmap'] = cmap
    if marker_size:
        kwargs['node_size'] = marker_size
    return kwargs 
```

现在这样好多了！

我根本不用改函数体，函数签名的改动我也能接受。* *除了更改参数名之外，kwargs 是唯一添加到签名中的内容。

当完全消除这种不赞成的时候，原来的函数将不会被干涉。

我添加了一个测试(我认为这是一个集成测试，但是语义对于这篇文章并不重要)。

```
def test_params_deprecation_view_connectome():
    deprecated_params = {'coords': 'node_coords',
                         'threshold': 'edge_threshold',
                         'cmap': 'edge_cmap',
                         'marker_size': 'node_size',
                         }
    deprecation_msg = (
        'The parameter "{}" will be removed in Nilearn version 0.6.0\. '
        'Please use the parameter "{}" instead.'
    )
    warning_msgs = {old_: deprecation_msg.format(old_, new_)
                    for old_, new_ in deprecated_params.items()
                    }

    adj, coord = _make_connectome()
    with warnings.catch_warnings(record=True) as raised_warnings:
        html_connectome.view_connectome(adjacency_matrix=adj,
                                        coords=coord,
                                        edge_threshold='85.3%',
                                        edge_cmap=cm.cyan_orange,
                                        linewidth=8.5, node_size=4.2,
                                        )

        html_connectome.view_connectome(adjacency_matrix=adj,
                                        node_coords=coord,
                                        threshold='85.3%',
                                        edge_cmap=cm.cyan_orange,
                                        linewidth=8.5,
                                        node_size=4.2,
                                        )

        html_connectome.view_connectome(adjacency_matrix=adj,
                                        node_coords=coord,
                                        edge_threshold='85.3%',
                                        cmap=cm.cyan_orange,
                                        linewidth=8.5,
                                        node_size=4.2,
                                        )

        html_connectome.view_connectome(adjacency_matrix=adj,
                                        node_coords=coord,
                                        edge_threshold='85.3%',
                                        edge_cmap=cm.cyan_orange,
                                        linewidth=8.5,
                                        marker_size=4.2,
                                        )

        html_connectome.view_connectome(adjacency_matrix=adj,
                                        node_coords=coord,
                                        edge_threshold='85.3%',
                                        edge_cmap=cm.cyan_orange,
                                        linewidth=8.5,
                                        node_size=4.2,
                                        )

        html_connectome.view_connectome(adj,
                                        coord,
                                        '85.3%',
                                        cm.cyan_orange,
                                        8.5,
                                        4.2,
                                        )
    old_params = ['coords', 'threshold', 'cmap', 'marker_size']

    assert len(raised_warnings) == 4
    for old_param_, raised_warning_ in zip(old_params, raised_warnings):
        assert warning_msgs[old_param_] == str(raised_warning_.message)
        assert raised_warning_.category is DeprecationWarning 
```

我认为那是，这是好的。

# 第四次迭代

几周后，我不得不在 Nilearn 中用另一个函数做同样的事情，改变它自己的一组参数，在 Nistats 的三个不同的函数中使用相同的参数。

这时，我决定通过让装饰器接受参数，使它成为一个通用的实用函数。

我还决定在 Nistats 中重用我即将为 Nilearn 编写的代码，以达到相同的目的，因为我们正在努力在不久的将来将 Nistats 库合并到 Nilearn 中，所以这很好。

我们没有寻找可能已经这样做的外部库，因为:

1.  我们不想在代码中引入任何不必要的依赖。
2.  我们不想为一个小功能安装一个库。
3.  一般来说，较小的(较少使用/流行的)库可能更容易停止维护。我没有寻找任何数据来支持这一点，但对我来说似乎是合理的。
4.  一般来说，Nilearn & Nistats 库的许多外部贡献者是碰巧编码的科学家，虽然他们是非常伟大的编码者，但是在组合中引入新的东西和库会增加贡献的障碍。

我决定为此创建一个名为`replace_parameters`的通用装饰器，并将其添加到我们的`nilearn/_utils/helpers.py`模块中。

这是最后一件事的代码:

```
def replace_parameters(replacement_params,
                       end_version='future',
                       lib_name='Nilearn',
                       ):
    """
    Decorator to deprecate & replace specified parameters
    in the decorated functions and methods
    without changing function definition or signature.

    Parameters
    ----------
    replacement_params : Dict[string, string]
        Dict where the key-value pairs represent the old parameters
        and their corresponding new parameters.
        Example: {old_param1: new_param1, old_param2: new_param2,...}

    end_version : str (optional) {'future' (default) | 'next' | <version>}
        Version when using the deprecated parameters will raise an error.
        For informational purpose in the warning text.

    lib_name: str (optional) (Default: 'Nilearn')
        Name of the library to which the decoratee belongs.
        For informational purpose in the warning text.
    """

    def _replace_params(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            _warn_deprecated_params(replacement_params, end_version, lib_name,
                                    kwargs
                                    )
            kwargs = _transfer_deprecated_param_vals(replacement_params,
                                                     kwargs
                                                     )
            return func(*args, **kwargs)

        return wrapper
    return _replace_params

def _warn_deprecated_params(replacement_params, end_version, lib_name, kwargs):
    """ For the decorator replace_parameters(),
        raises warnings about deprecated parameters.

    Parameters
    ----------
    replacement_params: Dict[str, str]
    Dictionary of old_parameters as keys with replacement parameters
    as their corresponding values.

    end_version: str
    The version where use of the deprecated parameters will raise an error.
    For informational purpose in the warning text.

    lib_name: str
    Name of the library. For informational purpose in the warning text.

    kwargs: Dict[str, any]
    Dictionary of all the keyword args passed on the decorated function.

    """
    used_deprecated_params = set(kwargs).intersection(replacement_params)
    for deprecated_param_ in used_deprecated_params:
        replacement_param = replacement_params[deprecated_param_]
        param_deprecation_msg = (
            'The parameter "{}" will be removed in {} release of {}. '
            'Please use the parameter "{}" instead.'.format(deprecated_param_,
                                                            end_version,
                                                            lib_name,
                                                            replacement_param,
                                                            )
        )
        warnings.filterwarnings('always', message=param_deprecation_msg)
        warnings.warn(category=DeprecationWarning,
                      message=param_deprecation_msg,
                      stacklevel=3)

def _transfer_deprecated_param_vals(replacement_params, kwargs):
    """ For the decorator replace_parameters(), reassigns new parameters
    the values passed to their corresponding deprecated parameters.

    Parameters
    ----------
    replacement_params: Dict[str, str]
    Dictionary of old_parameters as keys with replacement parameters
    as their corresponding values.

    kwargs: Dict[str, any]
    Dictionary of all the keyword args passed on the decorated function.

    Returns
    -------
    kwargs: Dict[str, any]
    Dictionary of all the keyword args to be passed on
    to the decorated function, with old parameter names
    replaced by new parameters, with their values intact.
    """
    for old_param, new_param in replacement_params.items():
        old_param_val = kwargs.setdefault(old_param, None)
        if old_param_val is not None:
            kwargs[new_param] = old_param_val
        kwargs.pop(old_param)
    return kwargs 
```

现在，我需要为这里的私有函数编写如此广泛的文档字符串吗？有些人可能会说不，我说是。

1.  内部/私有函数的详细 docstring 便于下一个加入团队的开发人员理解代码库，尤其是当最初的作者可能不在身边时。
2.  这让我编写单元测试变得更加容易，因为我知道预期会出现什么。docstring 在我心中澄清了这一切。

主要功能本身是这样的:

```
 def _replacement_params_view_connectome():
    """ Returns a dict containing deprecated & replacement parameters
        as key-value pair for view_connectome().
        Avoids cluttering the global namespace.
    """
    return {
        'coords': 'node_coords',
        'threshold': 'edge_threshold',
        'cmap': 'edge_cmap',
        'marker_size': 'node_size',
        }

@replace_parameters(replacement_params=_replacement_params_view_connectome(),
                    end_version='0.6.0',
                    lib_name='Nilearn',
                    )
def view_connectome(adjacency_matrix, node_coords, edge_threshold=None,
                    edge_cmap=cm.bwr, symmetric_cmap=True,
                    linewidth=6., node_size=3.,
                    ):

    connectome_info = _get_connectome(
        adjacency_matrix, node_coords, threshold=edge_threshold, cmap=edge_cmap,
        symmetric_cmap=symmetric_cmap, marker_size=node_size)
    return _make_connectome_html(connectome_info) 
```

Woohoo!

1.  除了对我们试图重命名的东西进行重命名之外，不必对函数体进行更改。
2.  除了重命名我们要重命名的参数之外，函数签名不需要做任何更改。事实证明，对于装饰者，我不需要显式的`**kwargs`。
3.  该功能易于重用、干净、可测试(和测试)。

我决定保留我已经编写的集成测试，并为这个新代码添加单元和集成测试。

```
from nilearn._utils.helpers import replace_parameters

def _mock_args_for_testing_replace_parameter():
    """
    :return: Creates mock deprecated & replacement parameters for use with
    testing functions related to replace_parameters().
    """
    mock_kwargs_with_deprecated_params_used = {
        'unchanged_param_0': 'unchanged_param_0_val',
        'deprecated_param_0': 'deprecated_param_0_val',
        'deprecated_param_1': 'deprecated_param_1_val',
        'unchanged_param_1': 'unchanged_param_1_val',
        }
    replacement_params = {
        'deprecated_param_0': 'replacement_param_0',
        'deprecated_param_1': 'replacement_param_1',
        }
    return mock_kwargs_with_deprecated_params_used, replacement_params

def test_replace_parameters():
    """ Integration tests that deprecates mock parameters in a mock function
    and checks that the deprecated parameters transfer their values correctly
    to replacement parameters and all deprecation warning are raised as
    expected.
    """
    mock_input, replacement_params = _mock_args_for_testing_replace_parameter()
    expected_output = ('dp0', 'dp1', 'up0', 'up1')
    expected_warnings = [
        ('The parameter "deprecated_param_0" will be removed in 0.6.1rc '
         'release of other_lib. Please use the parameter "replacement_param_0"'
         ' instead.'
         ),
        ('The parameter "deprecated_param_1" will be removed in 0.6.1rc '
         'release of other_lib. Please use the parameter "replacement_param_1"'
         ' instead.'
         ),
        ]

    @replace_parameters(replacement_params, '0.6.1rc', 'other_lib', )
    def mock_function(replacement_param_0, replacement_param_1,
                      unchanged_param_0, unchanged_param_1):
        return (replacement_param_0, replacement_param_1, unchanged_param_0,
                unchanged_param_1
                )

    with warnings.catch_warnings(record=True) as raised_warnings:
        actual_output = mock_function(deprecated_param_0='dp0',
                                      deprecated_param_1='dp1',
                                      unchanged_param_0='up0',
                                      unchanged_param_1='up1',
                                      )

    assert actual_output == expected_output

    expected_warnings.sort()
    raised_warnings.sort(key=lambda mem: str(mem.message))
    for raised_warning_, expected_warning_ in zip(raised_warnings,
                                                  expected_warnings):
        assert str(raised_warning_.message) == expected_warning_

def test_transfer_deprecated_param_vals():
    """ Unit test to check that values assigned to deprecated parameters are
    correctly reassigned to the replacement parameters.
    """
    mock_input, replacement_params = _mock_args_for_testing_replace_parameter()
    expected_output = {
        'unchanged_param_0': 'unchanged_param_0_val',
        'replacement_param_0': 'deprecated_param_0_val',
        'replacement_param_1': 'deprecated_param_1_val',
        'unchanged_param_1': 'unchanged_param_1_val',
        }
    actual_ouput = helpers._transfer_deprecated_param_vals(
            replacement_params,
            mock_input,
            )
    assert actual_ouput == expected_output

def test_future_warn_deprecated_params():
    """ Unit test to check that the correct warning is displayed.
    """
    mock_input, replacement_params = _mock_args_for_testing_replace_parameter()
    expected_warnings = [
        ('The parameter "deprecated_param_0" will be removed in sometime '
         'release of somelib. Please use the parameter "replacement_param_0" '
         'instead.'
         ),
        ('The parameter "deprecated_param_1" will be removed in sometime '
         'release of somelib. Please use the parameter "replacement_param_1" '
         'instead.'
         ),
        ]
    with warnings.catch_warnings(record=True) as raised_warnings:
        helpers._warn_deprecated_params(
                replacement_params,
                end_version='sometime',
                lib_name='somelib',
                kwargs=mock_input,
                )
    expected_warnings.sort()
    raised_warnings.sort(key=lambda mem: str(mem.message))
    for raised_warning_, expected_warning_ in zip(raised_warnings,
                                                  expected_warnings
                                                  ):
        assert str(raised_warning_.message) == expected_warning_ 
```

这就是我解决任务的方式，以及引导我找到解决方案的循序渐进的思路。
我在这里添加了最终的代码和测试作为要诀:
[https://gist . github . com/kchawla-pi/40 a 08 a 18 DC 04 f 39 CD 338 a4 CDC 15 EB 6a 9](https://gist.github.com/kchawla-pi/40a08a18dc04f39cd338a4cdc15eb6a9)

* * *

更新:修正了代码样本中几个错误命名的参数和几个错别字，我希望这将一直是正在进行的工作。
在最终测试中增加了 missing _ mock _ args _ for _ testing _ replace _ parameter()。