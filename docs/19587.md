# 你想知道的关于 GraphQL 的一切

> 原文：<https://dev.to/takeshape/all-you-ever-wanted-to-know-about-graphql-323h>

TakeShape 的联合创始人 Andrew Sprouse 在纽约的 JAMstack 会议上展示 GraphQL。

[https://www.youtube.com/embed/FgjHCt_9do4](https://www.youtube.com/embed/FgjHCt_9do4)

**伴随 Github 回购:[https://github.com/takeshape/jamstack-meetup-graphql-demo](https://github.com/takeshape/jamstack-meetup-graphql-demo)T3】**

### 什么是 GraphQL？

模式定义+查询语言+解析框架

### 图式

*   提供数据的强类型描述
*   模式描述语言(SDL)是推荐的跨平台模式描述方式。

```
enum  Title  {  ACTOR  DIRECTOR  }  type  Role  {  characterName:  String!  title:  Title  movie:  Movie  }  type  Person  {  name:  String!  photo:  String  filmography:  [Role]  }  type  Movie  {  title:  String!  watched:  Boolean  rating:  Float  poster:  String  actors:  [Person]!  director:  Person!  year:  String  } 
```

GraphQL 模式还指定了如何通过查询和变异与数据进行交互

```
type  Query  {  listMovies:  [Movie]  }  type  Mutation  {  addMove(title:  String):  [Movie]  removeMovie(title:  String):  [Movie]  } 
```

### 查询语言

查询您的数据并准确获取您需要的内容

```
query  {  getToWatchList  {  watched  movie  {  title  year  director  }  }  } 
```

```
{  "data":  {  "getToWatchList":  [  {  "watched":  true,  "movie":  {  "title":  "Top Gun",  "year":  "1985",  "director":  "Tony Scott"  }  },  {  "watched":  true,  "movie":  {  "title":  "Big Trouble in Little China",  "year":  "1986",  "director":  "John Carpenter"  }  },  {  "watched":  true,  "movie":  {  "title":  "The Princess Bride",  "year":  "1987",  "director":  "Rob Reiner"  }  },  {  "watched":  false,  "movie":  {  "title":  "Taxi Driver",  "year":  "1976",  "director":  "Martin Scorsese"  }  }  ]  }  } 
```

### 突变

修改您的数据

```
mutation  {  addMovieToWatch(title:  "Die Hard")  {  watched  movieTitle  movie  {  title  year  director  }  }  } 
```

```
{  "data":  {  "addMovieToWatch":  [  {  "watched":  false,  "movie":  {  "title":  "Taxi Driver",  "year":  "1976",  "director":  "Martin Scorsese"  }  },  {  "watched":  false,  "movie":  {  "title":  "Die Hard",  "year":  "1988",  "director":  "John McTiernan"  }  }  ]  }  } 
```

### 解析框架

*   每个 GraphQL 实现都提供了自己的查询解析框架
*   GraphQL.js 是参考实现
*   执行查询和变异解析

#### 图式(SDL)

```
type  Query  {  getToWatchList:  [ToWatch]  }  type  Mutation  {  addMovieToWatch(title:  String!):  [ToWatch]  removeMovieToWatch(title:  String!):  [ToWatch]  markMovieWatched(title:  String!  watched:  Boolean!):  [ToWatch]  } 
```

#### 解析器

```
const resolvers = {
    Query: {
        getToWatchList: () => {
            return WatchList.list();
        }
    },

    Mutation: {
        addMovieToWatch(_, {title}) {
            return WatchList.add(title);
        },

        removeMovieToWatch(_, {title}) {
            return WatchList.remove(title);
        },

        markMovieWatched(_, {title, watched}) {
            return WatchList.markWatched(title, watched);
        }
    }
}; 
```

解析器还能够解析动态计算的字段

#### 图式(SDL)

```
type  Move  {  title:  String  rating:  Float  poster:  String  actors:  String  director:  String  year:  String  }  type  ToWatch  {  movieTitle:  String!  movie:  Movie  watched:  Boolean!  } 
```

#### 解析器

```
const resolvers = {
    ToWatch: {
        async movie(toWatch) {
            const info = await fetchMovieInfo(toWatch.movieTitle);
            return info ? {
                title: info.Title,
                rating: info.imdbRating,
                poster: info.Poster,
                year: info.Year,
                actors: info.Actors,
                director: info.Director
            } : null
        }
    }
}; 
```