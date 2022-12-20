# (缺少)关键字库文件系统事件

> 原文：<https://dev.to/jochemstoel/the-lack-of-keybase-filesystem-events-315b>

对于我的一个客户(小公司)来说，当有人在他们共享的 [Keybase](https://keybase.io/) 团队文件夹中添加、删除或重命名文件时，通知员工似乎是一个有用的功能，这样他们就不必每次都检查。

这个想法源于我看到在 Keybase GUI 的紧凑窗口中，您可以看到最近文件更改的列表。我[问 Keybase support](https://github.com/keybase/keybase-issues/issues/3314) 我是否可以从某个地方读取事件。毕竟是电子应用。
keybase 文件系统没有*实现任何*真实事件，所以我创建了一个模拟它们的小脚本。

我写了一个粗糙的小脚本，[globs](https://www.npmjs.com/package/glob-cmd)k:/team/name 文件夹并发出更改。我只是作为一种 cronjob 定期(每 30 秒)运行这个脚本。

分解一下:

*   glob 个人或团队文件夹
*   将结果与上次运行进行比较
*   如果添加了一个新文件，发出一个*创建*事件
*   如果一个文件不再存在，发出一个*删除*事件
*   如果文件的 mtime(最后修改时间)发生了变化，则发出 *change* 事件

检查一个文件是否已被重命名有点棘手，因为旧文件将被检测为丢失/删除，而重命名的文件将被视为新文件。为了做到这一点，当一个文件被创建而另一个文件被删除时，比较新文件和旧文件的 mtime。如果它们相同，则很可能已经被重命名。(想不出太多例外)

我知道...它很粗糙，但是嘿；但是下面这个已经在所有操作系统上测试过了，总比没有好。

```
/* filesystem I/O and child process */
const fs = require('fs')
const childProcess = require('child_process')

/* events */
var Emitter = require('./emitter')
var emitter = new Emitter() 

/* subscribe to all events and use console.log as temporary callback */
emitter.on('*', console.log) // probably pipe to some GUI / notification

/* buffer file for file list */
const oldFiles = 'teamname.json'
/* the directory to watch */
let directory = 'k:/team/teamname/**/*'

/* buffer file otherwise initialize empty array */
let old = fs.existsSync(oldFiles) ? JSON.parse(fs.readFileSync(oldFiles, 'utf8')) : []

/* normalize object with lstat data to JSON compatible format */
const normalize = files => JSON.parse(JSON.stringify(files))

/* start time */
let start = Date.now()

/* match all files and directories */
let files = normalize(JSON.parse(childProcess.execSync(`glob --json "${directory}"`).toString()).map(file => [file, fs.lstatSync(file).mtime]))

/* time it took = end time - start time */
let end = Date.now()
let difference = end - start

emitter.emit('done', { took: difference/1000 }) // seconds

Array.prototype.has = function(file) {
    for(let item of this) {
        if(item[0]==file[0])
        return true
    }
    return false
}

Array.prototype.hasmtime = function(mtime) {
    for(let item of this) {
        if(item[1]==mtime)
        return item
    }
    return false
}

let newfiles = []

/* iterate old files */
old.forEach((file, index) => {
    let [f, mtime] = file 
    let fl 
    /* find new file with filename = this old filename */
    fl = files.filter(file => file[0] == f)
    /* because map returns array */
    if(fl.length > 0) { 
        fl = fl[0]
        /* if mtime has changed, emit change event */
        if(mtime!=fl[1])
            emitter.emit('changed', fl)
    }
    /* if new files does not have this current old file */
    if(!files.has(file)) {
        /* check to see if a file with exactly the same mtime exists */
        let changed = files.hasmtime(file[1])
        if(changed) {
            /* veeery likely that file is renamed */
            emitter.emit('rename', { from: file[0], to: changed[0]})
        } else {
            /* file no longer exists and no same mtime file found */
            emitter.emit('deleted', file)
        }
    }
})

/* if a new file does not exist anywhere in old files */
files.forEach(file => !old.has(file) ? (emitter.emit('new', file) && newfiles.push(file)) : null)

/* finally, write the new files to file for next iteration */
fs.writeFileSync(oldFiles, JSON.stringify(files)) 
```

用 setInterval 把它包起来，瞧！

注意:根据你的网速和文件/目录的数量，大概需要 5 到 15 秒的时间来创建整个文件夹。在决定每次运行之间的延迟时要考虑到这一点。此外，即时通知并不总是(几乎从来没有)那么重要。(30 秒到 1 分钟是可以接受的)

注意:你可以在本文中找到我使用的发射器类[。](https://dev.to/jochemstoel/write-your-own-chainable-event-emitter-class-for-node-and-browser-5gp)

注意:我将它用于 keybase，但它也适用于本地文件夹、FTP 和共享网络文件夹。

Todo:您不仅可以通过检查上次修改时间，还可以通过检查文件大小是否相同来提高文件被重命名的确定性。您可以从 *lstat* 读取文件大小，就像上次修改时间一样。