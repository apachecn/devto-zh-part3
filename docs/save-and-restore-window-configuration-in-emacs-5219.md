# 在 Emacs 中保存和恢复窗口配置

> 原文：<https://dev.to/erickgnavar/save-and-restore-window-configuration-in-emacs-5219>

当我们使用`emacs`时，有时我们经常同时在不同的窗口中打开许多缓冲区，例如我们可以有一个缓冲区用于打开文件，另一个用于查看测试结果等等。

有一些工具可以管理“会话”,但是我想要简单的东西，我也想多学一点儿`elisp`,所以下面是结果。

```
(defvar window-snapshots '())

  (defun save-window-snapshot ()
    "Save the current window configuration into `window-snapshots` alist."
    (interactive)
    (let ((key (read-string "Enter a name for the snapshot: ")))
      (setf (alist-get key window-snapshots) (current-window-configuration))
      (message "%s window snapshot saved!" key)))

  (defun get-window-snapshot (key)
    "Given a KEY return the saved value in `window-snapshots` alist."
    (let ((value (assoc key window-snapshots)))
      (cdr value)))

  (defun restore-window-snapshot ()
    "Restore a window snapshot from the window-snapshots alist."
    (interactive)
    (let* ((snapshot-name (completing-read "Choose snapshot: " (mapcar #'car window-snapshots)))
           (snapshot (get-window-snapshot snapshot-name)))
      (if snapshot
          (set-window-configuration snapshot)
        (message "Snapshot %s not found" snapshot-name)))) 
```

代码主要做三件事:

*   定义一个保存窗口配置的`alist`
*   使用稍后识别的名称保存当前窗口配置。
*   通过从列表中选择来恢复任何保存的配置

现在让我们进入细节:

## 保存当前窗口配置

我们将使用函数`current-window-configuration`来获取窗口的当前状态，并将该值放入名为`window-snapshots`的`alist`中。此外，它应该要求一个名称，以便我们可以稍后搜索它。

```
(defvar window-snapshots '())

  (defun save-window-snapshot ()
    "Save the current window configuration into `window-snapshots` alist."
    (interactive)
    (let ((key (read-string "Enter a name for the snapshot: ")))
      (setf (alist-get key window-snapshots) (current-window-configuration))
      (message "%s window snapshot saved!" key))) 
```

## 恢复一个窗口的配置

我们将使用`completing-read`从交互列表中选择一个保存的快照。它将使用`helm` <sup id="fnref:helm">[1](#fn:helm)</sup> 或`ivy` <sup id="fnref:ivy">[2](#fn:ivy)</sup> 如果安装了其中任何一个，否则它将在`minibuffer`中显示选项。

这将显示之前使用的`name`,并检索窗口配置的值。然后，它将使用`set-window-configuration`应用配置。

```
(defun get-window-snapshot (key)
    "Given a KEY return the saved value in `window-snapshots` alist."
    (let ((value (assoc key window-snapshots)))
      (cdr value)))

  (defun restore-window-snapshot ()
    "Restore a window snapshot from the window-snapshots alist."
    (interactive)
    (let* ((snapshot-name (completing-read "Choose snapshot: " (mapcar #'car window-snapshots)))
           (snapshot (get-window-snapshot snapshot-name)))
      (if snapshot
          (set-window-configuration snapshot)
        (message "Snapshot %s not found" snapshot-name)))) 
```

* * *

1.  [https://emacs-helm.github.io/helm/](https://emacs-helm.github.io/helm/)T2【归来】
2.  [https://github.com/abo-abo/swiper/](https://github.com/abo-abo/swiper/)T2【归来】