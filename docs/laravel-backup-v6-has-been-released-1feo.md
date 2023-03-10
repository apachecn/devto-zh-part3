# ★ laravel-backup v6 已经发布

> 原文：<https://dev.to/freekmurze/laravel-backup-v6-has-been-released-1feo>

laravel-backup 是一个 laravel 包，可以备份你的应用。

它可以创建一个 zip 文件，其中包含您的数据库的转储以及您可以选择的其他文件。利用 Laravel 的文件系统抽象的力量，这个 zip 文件可以上传到一个或多个远程文件系统。该软件包还可以监控备份的健康状况，并在出现任何问题时发出通知。

我们最近发布了 laravel-backup 的 v6 版本。虽然这是一个新的主要版本，但它主要是内部清理。唯一增加的新特性是支持[创建自定义健康检查](https://docs.spatie.be/laravel-backup/v6/monitoring-the-health-of-all-backups/creating-your-custom-health-check)。

## 支持自定义健康检查

在以前版本的程序包中，运行状况由上次备份的大小和时间决定。如果备份太大或者上次备份已经过去一天了，我们会发送通知。

在新版本的软件包中，您可以编写自己的健康检查。例如，您可以检查备份的大小是否增长。如果它没有随着时间的推移变大，可能是出了问题，可以发送通知。

写你自己的健康检查是相当容易的。您需要创建一个实现`Spatie\Backup\Tasks\Monitor\HealthCheck`的类，并在配置文件中注册您的类。想知道更多，请越过[去找](https://docs.spatie.be/laravel-backup/v6/monitoring-the-health-of-all-backups/creating-your-custom-health-check)的医生。

## 重写的测试

该软件包的第一个版本支持 Laravel 5.1。这个包的核心 testsuite 是用 Laravel 当时拥有的测试设施编写的。同时，Laravel 增加了许多细节:[伪造文件系统](https://laravel.com/docs/master/mocking#storage-fake)，[伪造通知](https://laravel.com/docs/master/mocking#notification-fake)，[测试控制台命令](https://laravel.com/docs/master/console-tests#expecting-input-and-output)，...

对于 laravel-backup 的 v6，我花时间重写了测试套件以利用这些新特性。

让我们看看这个包可以清理旧的备份。在此测试中，我们创建了 1000 个不同时间的备份。然后，我们运行`backup:clean`命令，验证一些备份仍然存在，一些备份已经被删除。

先说旧代码。为了简洁，我稍微编辑了一下。在这段旧代码中，我仍然需要自己设置一个临时目录并创建一个文件系统。

```
/** @test */
public function it_can_remove_old_backups_from_the_backup_directory()
{
    $allBackups = collect();

    collect(range(0, 1000))->each(function (int $numberOfDays) use ($allBackups) {
        $date = Carbon::now()->subDays($numberOfDays);

        $allBackups->push($this->testHelper->createTempFileWithAge("mysite/test_{$date->format('Ymd')}_first.zip", $date));
        $allBackups->push($this->testHelper->createTempFileWithAge("mysite/test_{$date->format('Ymd')}_second.zip", $date->addHour(2)));
    });

    $remainingBackups = collect([
        'mysite/test_20131231_first.zip',
        'mysite/test_20141231_first.zip',
        'mysite/test_20150630_first.zip',
        // ...
    ]);

    Artisan::call('backup:clean');

    $this->assertTempFilesExist($remainingBackups->toArray());

    $deletedBackups = $allBackups
        ->map(function ($fullPath) {
            $tempPath = str_replace($this->testHelper->getTempDirectory().'/', '', $fullPath);

            return $tempPath;
        })
        ->reject(function (string $deletedPath) use ($remainingBackups) {
            return $remainingBackups->contains($deletedPath);
        });

    $this->assertTempFilesNotExist($deletedBackups->toArray());
} 
```

这是新版本。在这里，我们可以使用一个伪造的文件系统，并使用一些添加到最近的 Laravel 版本中的很酷的收集方法。不是代码行少了，而是代码的意图更明确了。

```
/** @test */
public function it_can_remove_old_backups_from_the_backup_directory()
{
    $this->setNow(2016, 1, 1);

    [$expectedRemainingBackups, $expectedDeletedBackups] = Collection::times(1000)
        ->flatMap(function (int $numberOfDays) {
            $date = now()->subDays($numberOfDays);

            return [
                $this->createFileOnDisk('local', "mysite/test_{$date->format('Ymd')}_first.zip", $date),
                $this->createFileOnDisk('local', "mysite/test_{$date->format('Ymd')}_second.zip", $date->addHour(2)),
            ];
        })->partition(function (string $backupPath) {
            return in_array($backupPath, [
                'mysite/test_20131231_first.zip',
                'mysite/test_20141231_first.zip',
                'mysite/test_20150630_first.zip',
                // ... 
            ]);
        });

    $this->artisan('backup:clean')->assertExitCode(0);

    Storage::disk('local')
       ->assertExists($expectedRemainingBackups->toArray())
       ->assertMissing($expectedDeletedBackups->toArray());
} 
```

很高兴看到添加到 Laravel 中的特性如何使我能够编写更清晰的代码。虽然普通用户永远看不到它们，但是这些重写的测试将会改善贡献者在处理包时的体验。

## 在关闭

不要认为使用你的主机提供商的备份就足够了。他们也会搞砸。如果你想了解更多关于 laravel-backup 本身的特性，去阅读[文档](https://docs.spatie.be/laravel-backup/v6/introduction)。