# 如何修复 Rails active record::ConcurrentMigrationError

> 原文：<https://dev.to/dx0x58/how-to-fix-rails-activerecord-concurrentmigrationerror-1g65>

要在尝试运行 Rails 数据库迁移时纠正`ActiveRecord::ConcurrentMigrationError`，您需要:

1.  打开`psql`
2.  选择咨询锁`SELECT pid, locktype, mode FROM pg_locks WHERE locktype = 'advisory';`
3.  杀了这个恶棍！`SELECT pg_terminate_backend(<PID>);`