# #TIR:使用日志在 Elixir 中构建一个简单的持久性键值存储——第 1 部分

> 原文：<https://dev.to/chenge/recommend-build-a-simple-persistent-key-value-store-in-elixir-using-logs---part-1-cbe>

[链接- >](https://www.poeticoding.com/build-a-simple-persistent-key-value-store-in-elixir-using-logs-part-1/)

今天我从超赞的灵药时事通讯上读到了它。很适合学习长生不老药。

```
def lookup(key) do
  GenServer.call(__MODULE__, {:lookup, key})
end

def handle_call({:lookup, key}, _from, index_map) do
  {:reply, get_key_offset_size(key, index_map), index_map}
end

defp get_key_offset_size(key, index_map) do
  case Map.get(index_map, key) do
    {_offset, _size} = offset_size -> {:ok, offset_size}
    nil -> {:error, :not_found}
  end
end 
```