# 如何在 Swift 4 中以编程方式创建具有标准布局的 UICollectionView？

> 原文：<https://dev.to/danpgomez/how-do-i-create-a-uicollectionview-with-standard-layout-programmatically-in-swift-4-9mh>

大家好！

我是 iOS 开发的新手，大部分时间都在使用界面构建器。然而，我最近开始学习如何以编程方式创建 UI 元素，但是我发现很少有教程是关于不用 IB 来做事情的。

我使用 YouTube 教程拼凑了这篇文章底部的代码，但是，当它编译和运行时，我有几个关于它如何工作和为什么工作的问题:

1)我注意到集合视图是这样声明的:

```
 let myCollectionView: UICollectionView = {}() 
```

Enter fullscreen mode Exit fullscreen mode

在阅读了 Swift.org 的[属性](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)页面后，该格式看起来类似于计算属性。是这样吗？为什么括号后面有圆括号？我们是在“调用”集合视图还是实例化它？

2)当集合视图不需要自定义布局时，是否使用`UICollectionViewFlowLayout`？意思是，这是“默认”布局吗？您是否总是“必须”为所有集合视图定义一个布局对象？

3)`let cv = UICollectionView(frame: .zero, collectionViewLayout: layout)`行中的`frame: .zero`是什么意思？

4)如何在集合视图单元格中添加元素，如标题？

非常感谢任何建议或指导！😀

```
 import UIKit

class FirstVC: UIViewController, UICollectionViewDelegate, UICollectionViewDataSource, UICollectionViewDelegateFlowLayout {

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        view.addSubview(collectionView)
        collectionView.delegate = self
        collectionView.dataSource = self
        collectionView.register(UICollectionViewCell.self, 
        forCellWithReuseIdentifier: "cell")
        setupCollectionConstraints()
    }

    let collectionView: UICollectionView = {
        let layout = UICollectionViewFlowLayout()
        layout.scrollDirection = .horizontal
        let cv = UICollectionView(frame: .zero, collectionViewLayout: layout)
        cv.backgroundColor = .lightGray
        return cv
    }()

    func setupCollectionConstraints() {
        collectionView.translatesAutoresizingMaskIntoConstraints = false
        collectionView.topAnchor.constraint(equalTo: view.topAnchor, constant: 100).isActive = true
        collectionView.heightAnchor.constraint(equalToConstant: 200).isActive = true
        collectionView.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
        collectionView.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true
    }

    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 2
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell  = collectionView.dequeueReusableCell(withReuseIdentifier: "cell", for: indexPath)
        cell.backgroundColor = .white
        return cell
    }

    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        return CGSize(width: 250, height: 100)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode