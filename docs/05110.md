# 如何在 RxSwift 中测试 PublishSubject

> 原文：<https://dev.to/onmyway133/how-to-test-publishsubject-in-rxswift-57b9>

*   RxBlocking 不适用于变量和 PublishSubject，请参见[https://github . com/react vex/rx swift/blob/0 b 66 f 666 ba 6955 a 51 CBA 1 ad 530311 b 030 fa 4 db 9 c/Tests/RxSwiftTests/Observable % 2b subscription test . swift # L165](https://github.com/ReactiveX/RxSwift/blob/0b66f666ba6955a51cba1ad530311b030fa4db9c/Tests/RxSwiftTests/Observable%2BSubscriptionTest.swift#L165)

## 用自制的`Recorder`

```
class Recorder<T> {
    var items = [T]()
    let bag = DisposeBag()

    func on(arraySubject: PublishSubject<[T]>) {
        arraySubject.subscribe(onNext: { value in
            self.items = value
        }).disposed(by: bag)
    }

    func on(valueSubject: PublishSubject<T>) {
        valueSubject.subscribe(onNext: { value in
            self.items.append(value)
        }).disposed(by: bag)
    }
} 
```

然后测试

```
final class BookViewModelTests: XCTestCase {
    func testBooks() throws {
        let expectation = self.expectation(description: #function)
        let recorder = Recorder<Book>()
        let viewModel = BookViewModel(bookClient: MockBookClient())
        recorder.on(arraySubject: viewModel.books)
        viewModel.load()

        DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + 0.5, execute: {
            expectation.fulfill()
        })

        wait(for: [expectation], timeout: 2)
        XCTAssertEqual(recorder.items.count, 3)
    }
} 
```

需要使用大的`timeout`值，因为`DispatchQueue`不能保证精确，一个块需要等待队列为空才能被执行

## 让`expectation`不那么繁琐

```
extension XCTestCase {
    func waitOrFail(timeout: TimeInterval) {
        let expectation = self.expectation(description: #function)

        DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + timeout, execute: {
            expectation.fulfill()
        })

        wait(for: [expectation], timeout: timeout + 2)
    }
} 
```

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️