# 卡在 this . props . adddog(this . state . newdog)的意思上

> 原文：<https://dev.to/giuseppe/stuck-on-the-meaning-of-this-props-adddog-this-state-newdog-3pnc>

我正在研究一个 React 应用程序，它由一个主要组件:App.js(主要组件)和另外三个外部组件:Dog.js、DogItem.js、AddDog.js 组成。该应用程序包含一组项目(Dogs): Dog.js，由单个 Dog 元素 DogItem.js 和一个表单:AddDog.js 组成，用于添加一个新项目:Dog。在 AddDog.js 文件中，我唯一没有意识到的一行是 this . props . adddog(this . state . newdog)；我在下面重点强调了一下。

我想在 this . props . addDog(this . state . newdog)里给那个 addDog 加下划线；与组件 AddDog 的名称不同。谢谢你。

下面是 AddDog.js

```
import React, { Component } from 'react';

class AddDog extends Component {
  constructor() {
    super();
    this.state = {
      newDog:{}
    }
  }

  static defaultProps = {
    categories: ['Web Design', 'Web Development', 'Mobile Development']
  }

  handleSubmit(e) {
    if(this.refs.name.value === '') {
      alert('Title is required');
    } else if (this.refs.image.value === '') {
        alert('Image link is required');
    } else if (this.refs.breed.value === '') {
          alert('Breed is required');
    } else {
      this.setState({newDog:{
        name: this.refs.name.value,
        breed: this.refs.breed.value,
        image: this.refs.image.value
      }}, function() {
        this.props.addDog(this.state.newDog); // <<<<<<<<<<<<<<<<<
      });
    }
    e.preventDefault();
  }

  render() {
    return (
      <div>
        <h3 id="addDog">Add Dog</h3>
        <form onSubmit={this.handleSubmit.bind(this)}>
          <div>
            <label>Name</label><br />
            <input id="dogName" type="text" ref="name" />
          </div>
          <div>
            <label>Image</label><br />
            <input id="imageURL" type="text" ref="image" />
          </div>
          <div>
            <label>Breed</label><br />
            <input id="dogBreed" type="text" ref="breed" />
          </div>
          <br />
          <input id="submitButton" type="submit" value="Submit" />
          <br />
        </form>
      </div>
    );
  }
}

export default AddDog; 
```

这里是 App.js

```
import React, { Component } from 'react';
import Dogs from './components/Dogs';
import DogItem from './components/DogItem';
import AddDog from './components/AddDog';
import './App.css';

class App extends Component {
  constructor() {
    super();
    this.state = {
      dogs: []
    };
  }

  getDogs() {
    var defaultDogs = {dogs: [
      {
        name: 'Princess',
        breed: 'Corgi',
        image: 'https://s-media-cache-ak0.pinimg.com/originals/51/ae/30/51ae30b78696b33a64661fa3ac205b3b.jpg'
      },
      {
        name: 'Riley',
        breed: 'Husky',
        image: 'http://portland.ohsohandy.cimg/uploads/93796/m/nice-and-sweet-siberian-husky-puppies-for-free-adoption.jpg'
      },
    ]}; 
    this.setState(defaultDogs);
  }

  componentWillMount() { // this soon display the two dogs before the render
    this.getDogs();  
  }

  handleAddDog(dog) {
    let dogs = this.state.dogs;
    dogs.push(dog);
    this.setState({dogs:dogs});
  }

  handleDeleteDog(name) {
    let dogs = this.state.dogs;
    let index = dogs.findIndex(x => x.name === name); // function (x) {return x.name === name} is like  x => x.name === name
    dogs.splice(index, 1);
    this.setState({dogs:dogs});
  }

  render() {
    return (
      <div className="App">
        <Dogs dogs={this.state.dogs} onDelete={this.handleDeleteDog.bind(this)} />
        <AddDog addDog={this.handleAddDog.bind(this)} />
        <hr />
      </div>
    );
  }
}

export default App; 
```

下面是 Dog.js

```
<pre>
import React, { Component } from 'react';
import DogItem from './DogItem';

class Dogs extends Component {
    deleteDog(name) {
        this.props.onDelete(name);
    }

    render() {
        let dogItem;
        if (this.props.dogs) {
            dogItem = this.props.dogs.map(dog => {
                return (
                    <DogItem onDelete={this.deleteDog.bind(this)} key={dog.name} dog={dog} />
                );
            });
        }
        return (
            <div className="Dogs">
                <h1>Good Dogs</h1>
                {dogItem}
            </div>
        );
    }
}

export default Dogs; 
```

以下是 DogItem.js

```
import React, { Component } from 'react';

class DogItem extends Component {
  deleteDog(name) {
    this.props.onDelete(name);
  }

  render() {
    return (
      <ul className="Dog">
        <img src={this.props.dog.image} href={this.props.dog.image} role="presentation"  width="100" height="100"></img>
        <br></br>
        <strong>{this.props.dog.name}</strong>: {this.props.dog.breed} <a href="#" onClick={this.deleteDog.bind(this, this.props.dog.name)}>X</a>
        <br></br>
      </ul>
    );
  }
}

export default DogItem; 
```