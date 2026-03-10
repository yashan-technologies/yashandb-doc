示例：基于Perl接口开发应用程序

```perl
#!/usr/bin/perl
# 演示基于Perl开发的主要步骤，涉及创建数据库连接、创建表、插入数据等。
use strict;
use DBI;

# 连接数据库
my $dbh = DBI->connect("DBI:ODBC:YASDBODBC", "sys", "Cod-2022", { 
    RaiseError => 1,
    AutoCommit => 1
});

# 执行普通SQL语句，创建users表
eval {
    $dbh->do("DROP TABLE IF EXISTS users");
    $dbh->do(<<'END_SQL');
CREATE TABLE users (
    id      INTEGER PRIMARY KEY,
    name    VARCHAR(60) NOT NULL,
    email   VARCHAR(100) UNIQUE,
    age     INTEGER
)
END_SQL
};
if ($@) {
    warn "创建表失败: $@";
}

# 插入数据
my $insert_stmt = qq{
    INSERT INTO users (id, name, email, age)
    VALUES (?, ?, ?, ?)
};
my $sth = $dbh->prepare($insert_stmt);

eval {
    # 插入第一条数据
    $sth->execute(1, 'Alice', 'alice@example.com', 30);
    
    # 插入第二条数据
    $sth->execute(2, 'Bob', 'bob@example.com', 25);
};
if ($@) {
    warn "插入数据失败: $@";
}


# 查询数据模块
sub get_data {
    my $select_stmt = qq{
        SELECT id, name, email, age
        FROM users
        WHERE age > ?
    };
    $sth = $dbh->prepare($select_stmt);
    $sth->execute(20);

    print "查询结果：\n";
    my $row;
    my $count=0;
    while ($row = $sth->fetchrow_arrayref()) {
        printf "ID: @$row[0], 姓名: @$row[1], 邮箱: @$row[2], 年龄: @$row[3]\n";
        $count += 1;
    }
    if ($count == 0) {
        print "no data\n";
    }
}


# 更新数据模块
sub update_data {
    my ($name) = @_;
    my $update_stmt = qq{
        UPDATE users
        SET age = age + 1
        WHERE name = ?
    };
    $sth = $dbh->prepare($update_stmt);
    $sth->execute($name);
    print("执行更新成功\n");
}


# 删除数据模块
sub delete_data {
    my ($email) = @_;
    my $delete_stmt = qq{
        DELETE FROM users
        WHERE email LIKE ?
    };
    $sth = $dbh->prepare($delete_stmt);
    $sth->execute($email);
    print("执行删除成功\n");
}

# 查询原始数据
get_data();

# 更新后查询数据，Alice的年龄+1
update_data('Alice');
get_data();

# 删除后查询数据，空表
delete_data('%example.com');
get_data();

# 关闭连接
$sth->finish;
$dbh->disconnect;
```

以Linux环境为例运行应用程序：

```shell
perl PerlExample.pl
```
