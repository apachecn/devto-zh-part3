# 引擎盖下的“全球纱线”( 2) -来源

> 原文：<https://dev.to/lucifer1004/yarn-global-under-the-hood-2----the-source-425k>

[![Yarn!](img/e2ac8433caef930c6aa06b7d3ac5644d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ibh68YF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yarnpkg.com/assets/feature-secure.png)

在之前的[帖子](https://dev.to/lucifer1004/yarn-global-under-the-hood-10nk)中，我们浏览了一下`yarn global`的安装目录，并了解了`yarn`如何作为一个项目管理我们的全球包。在本帖中，我们将看看`yarn`的相应源代码。

将要讨论的文件:

[`cli/commands/global.js`](https://github.com/yarnpkg/yarn/blob/master/src/cli/commands/global.js) ，其中定义了`yarn global`命令

## 指挥官旗帜

第一， [`global.js`](https://github.com/yarnpkg/yarn/blob/6c41eb140164aa4a3bc35a73b08f6d758fa24e62/src/cli/commands/global.js#L284) 结尾有我们熟悉的东西。如果你没有看过我之前在 NodeJS CLI 上的[帖子](https://dev.to/lucifer1004/developing-your-first-cli-command-line-interface-tool-using-nodejs-4649)，并且你想了解更多关于 commander 的信息，我强烈建议你去看看。

```
export function setFlags(commander: Object) {
  _setFlags(commander);
  commander.description('Installs packages globally on your operating system.');
  commander.option('--prefix <prefix>', 'bin prefix to use to install binaries');
  commander.option('--latest', 'upgrade to the latest version of packages');
} 
```

在这个片段中，设置了`yarn global`的描述，并增加了两个选项。我们可以在命令行中运行`yarn global -h`来显示它的帮助信息。输出将类似于:

```
yarn global -h

Usage: yarn global [add|bin|dir|ls|list|remove|upgrade|upgrade-interactive] [flags]

Installs packages globally on your operating system.

...

--prefix <prefix>                   bin prefix to use to install binaries
--latest                            upgrade to the latest version of packages
-h, --help                          output usage information
Visit https://yarnpkg.com/en/docs/cli/global for documentation about this command. 
```

你可以看到上面片段的效果。还有许多其他选项，它们继承自[全局](https://github.com/yarnpkg/yarn/blob/6c41eb140164aa4a3bc35a73b08f6d758fa24e62/src/cli/index.js#L62)上下文。

现在我们来看看[子命令](https://github.com/yarnpkg/yarn/blob/6c41eb140164aa4a3bc35a73b08f6d758fa24e62/src/cli/commands/global.js#L209)是如何构建的，也就是`yarn global add|bin|dir|ls|list|remove|upgrade|upgrade-interactive`，如上面的文本片段所示。

```
const {run, setFlags: _setFlags} = buildSubCommands('global', {
// The subcommands of `yarn global`
}) 
```

子命令都在这个函数中。从语义上来说，代码片段用于构建命令`global`的子命令。

## `yarn global add`

第一个子命令是`add`，这是最重要的一个，也是讨论最多的一个。

```
async add(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  await updateCwd(config);

  const updateBins = await initUpdateBins(config, reporter, flags);
  if (args.indexOf('yarn') !== -1) {
    reporter.warn(reporter.lang('packageContainsYarnAsGlobal'));
  }

  // install module
  const lockfile = await Lockfile.fromDirectory(config.cwd);
  const install = new GlobalAdd(args, flags, config, reporter, lockfile);
  await install.init();

  // link binaries
  await updateBins();
}, 
```

这个子命令处理一种奇怪的情况，用户试图通过条件`args.indexOf('yarn') !== -1`做类似于`yarn global add yarn`的事情。您可以尝试一下，输出会是这样的:

```
warning Installing Yarn via Yarn will result in you having two separate versions of Yarn installed at the same time, which is not recommended. To update Yarn please follow https://yarnpkg.com/en/docs/install . 
```

显然，`reporter.lang()`已经完成了从错误代码(`packageContainsYarnAsGlobal`)到人类可读警告消息的转换(如上所示)。

### `updateCwd()`

`updateCwd()`功能在 [`global.js`](https://github.com/yarnpkg/yarn/blob/6c41eb140164aa4a3bc35a73b08f6d758fa24e62/src/cli/commands/global.js#L46)
中定义

```
async function updateCwd(config: Config): Promise<void> {
  await fs.mkdirp(config.globalFolder);

  await config.init({
    cwd: config.globalFolder,
    binLinks: true,
    globalFolder: config.globalFolder,
    cacheFolder: config._cacheRootFolder,
    linkFolder: config.linkFolder,
    enableDefaultRc: config.enableDefaultRc,
    extraneousYarnrcFiles: config.extraneousYarnrcFiles,
  });
} 
```

它将尝试创建**全局文件夹**，然后返回全局文件夹的路径作为`cwd`。这样就保证了无论你当前在哪个目录下，`yarn global`的效果都不会有差别。

### `initUpdateBins()`

下一部分是`initUpdateBins()`函数。

```
async function initUpdateBins(config: Config, reporter: Reporter, flags: Object): Promise<() => Promise<void>> {
  const beforeBins = await getBins(config);
  const binFolder = await getBinFolder(config, flags);

  function throwPermError(err: Error & {[code: string]: string}, dest: string) {
    if (err.code === 'EACCES') {
      throw new MessageError(reporter.lang('noPermission', dest));
    } else {
      throw err;
    }
  }

  return async function(): Promise<void> {
    try {
      await fs.mkdirp(binFolder);
    } catch (err) {
      throwPermError(err, binFolder);
    }

    const afterBins = await getBins(config);

    // remove old bins
    for (const src of beforeBins) {
      if (afterBins.has(src)) {
        // not old
        continue;
      }

      // remove old bin
      const dest = path.join(binFolder, path.basename(src));
      try {
        await fs.unlink(dest);
      } catch (err) {
        throwPermError(err, dest);
      }
    }

    // add new bins
    for (const src of afterBins) {
      // insert new bin
      const dest = path.join(binFolder, path.basename(src));
      try {
        await fs.unlink(dest);
        await linkBin(src, dest);
        if (process.platform === 'win32' && dest.indexOf('.cmd') !== -1) {
          await fs.rename(dest + '.cmd', dest);
        }
      } catch (err) {
        throwPermError(err, dest);
      }
    }
  };
} 
```

If 首先检查当前二进制文件(`beforeBins`)和二进制文件文件夹(`binFolder`)，然后返回一个异步函数。

> 这样，返回的函数将把`beforeBins`和`binFolder`的值存储在闭包中。

当`const updateBins = await initUpdateBins(config, reporter, flags);`被执行时，`beforeBins`和`binFolder`被记录，但异步功能直到`Lockfile.fromDirectory`和`GlobalAdd`被执行后才会被执行。当 async 函数被实际调用时，新的包已经被安装到全局文件夹中，所以`getBins(config)`将返回一个新值，它被赋给`afterBins`。

异步函数试图创建**二进制文件夹**，当当前用户没有权限写入目标路径时抛出权限错误。然后它得到`afterBins`的值。

重点是`beforeBins`和`afterBins`的对比。这是通过两次迭代完成的。

在第一次迭代中，迭代`beforeBins`中的二进制文件。那些存在于`beforeBins`中而不在`afterBins`中的被解除链接——它们将被更新的版本所取代，而其余的保持不变——它们这次不涉及。

在第二次迭代中，`afterBins`中的所有二进制文件被解除链接，然后被链接。

> `unlink`适用于所有当前的符号链接，这似乎是不必要的。用意何在？是因为在第二次迭代中`unlink`比判断`beforeBins.has(src)`消耗的资源少吗？或者是因为即使新版本是一样的，如果没有删除原来的符号链接，可能会有问题？

### `getBins()`

```
async function getBins(config: Config): Promise<Set<string>> {
  // build up list of registry folders to search for binaries
  const dirs = [];
  for (const registryName of Object.keys(registries)) {
    const registry = config.registries[registryName];
    dirs.push(registry.loc);
  }

  // build up list of binary files
  const paths = new Set();
  for (const dir of dirs) {
    const binDir = path.join(dir, '.bin');
    if (!await fs.exists(binDir)) {
      continue;
    }

    for (const name of await fs.readdir(binDir)) {
      paths.add(path.join(binDir, name));
    }
  }
  return paths;
} 
```

`getBins()`函数在所有注册表中迭代，并收集所有二进制文件的路径的`Set`，它们位于每个注册表的`.bin`子目录中。

### `GlobalAdd`

它扩展了`Add`类，定义在 [`add.js`](https://github.com/yarnpkg/yarn/blob/master/src/cli/commands/add.js) 中，在`yarn add`之后。这再次揭示了`yarn global add`是`yarn add`的特例。

### `linkBin()`(在 [`package-linker.js`](https://github.com/yarnpkg/yarn/blob/master/src/package-linker.js) )

```
export async function linkBin(src: string, dest: string): Promise<void> {
  if (process.platform === 'win32') {
    const unlockMutex = await lockMutex(src);
    try {
      await cmdShim(src, dest);
    } finally {
      unlockMutex();
    }
  } else {
    await fs.mkdirp(path.dirname(dest));
    await fs.symlink(src, dest);
    await fs.chmod(dest, '755');
  }
} 
```

`yarn global add`使用该函数进行符号链接。在 Windows 的情况下，需要锁定和解锁，而对于 OSX 和 Linux，这个过程相当简单。

## `yarn global bin`

`bin`只是用来获取全局二进制文件夹。

```
async bin(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  reporter.log(await getBinFolder(config, flags), {force: true});
}, 
```

## `yarn global dir`

与`bin`类似，`dir`用于获取全局文件夹，其中放置了`yarn global`项目。

```
dir(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  reporter.log(config.globalFolder, {force: true});
  return Promise.resolve();
}, 
```

## `yarn global list`(及其弃用版本`yarn global ls`)

```
async ls(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  reporter.warn(`\`yarn global ls\` is deprecated. Please use \`yarn global list\`.`);
  await list(config, reporter, flags, args);
},

async list(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  await list(config, reporter, flags, args);
}, 
```

这两个子命令都调用`list`函数，该函数定义为:

```
async function list(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  await updateCwd(config);

  // install so we get hard file paths
  const lockfile = await Lockfile.fromDirectory(config.cwd);
  const install = new Install({}, config, new NoopReporter(), lockfile);
  const patterns = await install.getFlattenedDeps();

  // dump global modules
  for (const pattern of patterns) {
    const manifest = install.resolver.getStrictResolvedPattern(pattern);
    ls(manifest, reporter, false);
  }
} 
```

注意，它将首先执行没有标志的`install`(`{}`)。除非`package.json`或`yarn.lock`被手动或其他程序修改，否则不会安装任何软件包。正如评论所说，主要目的是获取文件路径。在`install.js`中，对于`getFlattenedDeps()`方法还有另外一个注释，说:

```
 /**
   * helper method that gets only recent manifests
   * used by global.ls command
   */
  async getFlattenedDeps(): Promise<Array<string>> {
    const {requests: depRequests, patterns: rawPatterns} = await this.fetchRequestFromCwd();

    await this.resolver.init(depRequests, {});

    const manifests = await fetcher.fetch(this.resolver.getManifests(), this.config);
    this.resolver.updateManifests(manifests);

    return this.flatten(rawPatterns);
  } 
```

然后，收集的模式将通过`ls()`函数:
进行迭代

```
function ls(manifest: Manifest, reporter: Reporter, saved: boolean) {
  const bins = manifest.bin ? Object.keys(manifest.bin) : [];
  const human = `${manifest.name}@${manifest.version}`;
  if (bins.length) {
    if (saved) {
      reporter.success(reporter.lang('packageInstalledWithBinaries', human));
    } else {
      reporter.info(reporter.lang('packageHasBinaries', human));
    }
    reporter.list(`bins-${manifest.name}`, bins);
  } else if (saved) {
    reporter.warn(reporter.lang('packageHasNoBinaries', human));
  }
} 
```

它将列出每个模式的二进制文件。注意当`ls()`在`list()`中被调用时`saved`被赋值为`false`，所以只有包含二进制文件的包会被列出，并且所有这样的包会被同等对待，所以所有的输出消息会像:

```
info "pm2@3.2.8" has binaries:
   - pm2
   - pm2-dev
   - pm2-docker
   - pm2-runtime 
```

## `yarn global remove`

```
async remove(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  await updateCwd(config);

  const updateBins = await initUpdateBins(config, reporter, flags);

  // remove module
  await runRemove(config, reporter, flags, args);

  // remove binaries
  await updateBins();
}, 
```

与`add`类似，`remove`利用`initUpdateBins`得到`beforeBins`和`afterBins`，然后进行解链接。模块的移除使用`remove.js`中的`runRemove()`功能。

## `yarn global upgrade`和`yarn global upgrade-interactive`

```
async upgrade(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  await updateCwd(config);

  const updateBins = await initUpdateBins(config, reporter, flags);

  // upgrade module
  await runUpgrade(config, reporter, flags, args);

  // update binaries
  await updateBins();
},

async upgradeInteractive(config: Config, reporter: Reporter, flags: Object, args: Array<string>): Promise<void> {
  await updateCwd(config);

  const updateBins = await initUpdateBins(config, reporter, flags);

  // upgrade module
  await runUpgradeInteractive(config, reporter, flags, args);

  // update binaries
  await updateBins();
}, 
```

`upgrade`和`upgrade-interactive`也和`remove`类似，分别使用`runUpgrade` (upgrade.js)和`runUpgradeInteractive`(upgrade-interactive . js)。

## 总结

现在我们已经过了`global.js`的源代码。编程没有什么魔力，你只需要深入钻研。