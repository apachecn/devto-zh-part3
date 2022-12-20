# 为获取请求从组件状态迁移到挂钩

> 原文：<https://dev.to/spencercarli/migrating-from-component-state-to-hooks-for-a-fetch-request-1bj1>

[https://www.youtube.com/embed/7LhcMx_w2Vg](https://www.youtube.com/embed/7LhcMx_w2Vg)

> 本教程最初发布于 [React 原生学校](https://www.reactnativeschool.com/migrating-from-component-state-to-hooks-for-a-fetch-request)。如果您有兴趣了解 React Native 的更多信息，请查看我们的 80 多门课程库！

钩子是 React Native 的最新热点。现在它们是 React Native core 的一部分了，这怎么能改变你的代码呢？

## 起始代码

```
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
  FlatList,
  SafeAreaView,
  ActivityIndicator,
  Button,
} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default class App extends React.Component {
  state = {
    people: [],
    loading: true,
    page: 1,
  };

  componentDidMount() {
    this.getPeople();
  }

  getPeople = () => {
    this.setState({ loading: true });
    fetch(`https://swapi.co/api/people?page=${this.state.page}`)
      .then(res => res.json())
      .then(res => {
        this.setState(state => ({
          people: [...state.people, ...res.results],
          loading: false,
        }));
      });
  };

  render() {
    return (
      <SafeAreaView style={{ flex: 1 }}>
        <FlatList
          data={this.state.people}
          keyExtractor={item => item.url}
          renderItem={({ item }) => (
            <View>
              <Text>{item.name}</Text>
            </View>
          )}
          ListFooterComponent={
            this.state.loading ? (
              <ActivityIndicator />
            ) : (
              <Button
                title="Load More"
                onPress={() => {
                  this.setState(
                    state => ({ page: state.page + 1 }),
                    this.getPeople
                  );
                }}
              />
            )
          }
        />
      </SafeAreaView>
    );
  }
} 
```

## 完成代码

```
const useSwapiPeople = () => {
  const [people, setPeople] = useState([]);
  const [loading, setLoading] = useState(false);
  const [page, setPage] = useState(1);

  useEffect(
    () => {
      setLoading(true);
      fetch(`https://swapi.co/api/people?page=${page}`)
        .then(res => res.json())
        .then(res => {
          setPeople([...people, ...res.results]);
          setLoading(false);
        });
    },
    [page]
  );

  const loadMore = () => {
    setPage(page + 1);
  };

  return {
    people,
    loading,
    loadMore,
  };
};

export default () => {
  const { people, loading, loadMore } = useSwapiPeople();

  return (
    <SafeAreaView style={{ flex: 1 }}>
      <FlatList
        data={people}
        keyExtractor={item => item.url}
        renderItem={({ item }) => (
          <View>
            <Text>{item.name}</Text>
          </View>
        )}
        ListFooterComponent={
          loading ? (
            <ActivityIndicator />
          ) : (
            <Button title="Load More" onPress={loadMore} />
          )
        }
      />
    </SafeAreaView>
  );
}; 
```