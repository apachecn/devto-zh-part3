# 如何在接口中定义一个带有输入参数的方法？

> 原文：<https://dev.to/bongbonglemon/how-do-i-define-a-method-with-input-parameters-in-an-interface-44j6>

我在这个链接上看了一个教程:[https://info448.github.io/fragment-viewpager.html](https://info448.github.io/fragment-viewpager.html)，内容是制作一个允许用户在片段间滑动的应用程序。

我设法完成了应用程序，但只是部分理解。我不明白片段通过片段类中定义的接口与包含活动通信的部分。

来自片段类:

```
 interface OnSearchListener {
        // allow the Fragment to pass the entered search term to the Activity
        void onSearchSubmitted(String searchTerm);
    }

    OnSearchListener mListener;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        View rootView = inflater.inflate(R.layout.fragment_search, container, false);

        mButton = (Button) rootView.findViewById(R.id.btn_search);
        editText = (EditText) rootView.findViewById(R.id.txt_search);

        mButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.onSearchSubmitted(editText.getText().toString());
            }
        });

        return rootView;
    } 
```

[https://thepractical dev . S3 . Amazon AWS . com/I/ixjgidx 1 jw 7 v4 iubj 0n 7 . png](https://thepracticaldev.s3.amazonaws.com/i/ixjgidx1jw7v4iubj0n7.png)

从活动类中:

```
 @Override
    public void onSearchSubmitted(String searchTerm) {
        mSearchTerm = searchTerm;
        numberOfPages = 2;
        // simply creating a different Fragment will not
        // cause the Adapter to change—you need to let the
        // Adapter know that the model it is adapting into has changed!
        adapter.notifyDataSetChanged();
        viewPager.setCurrentItem(1);
    } 
```

[https://thepractical dev . S3 . Amazon AWS . com/I/h 686 V5 sxwgfxea 0 gdk 76 . png](https://thepracticaldev.s3.amazonaws.com/i/h686v5sxwgfxea0gdk76.png)

如何在 Fragment 类中制作接口的实例变量，然后调用其方法 onSearchSubmitted(edittext . gettext()。toString())在 onClick 方法中，允许将输入参数传递给包含它的活动。

为了探索这一点，我去查看了其他@Override 方法，它们的输入参数似乎已经在 origin 接口中处理过了，比如
onCreate(Bundle savedInstanceState)。然而，我找不到 savedInstanceState 已经在框架中的什么地方被实例化并输入到接口中。

我也找不到 YouTube 教程或文章给出了以这种方式实现接口的例子，即拥有一个开发者不需要改变的带有输入参数的方法。

请帮忙，谢谢你看我的帖子。