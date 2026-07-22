***Example***: Developing Applications Based on Perl Interface

```perl
#!/usr/bin/perl
# Demonstrates the main steps of development in Perl, involving creating database connections, creating tables, inserting data, etc.
use strict;
use DBI;

# Connect to the database
my $dbh = DBI->connect("DBI:ODBC:YASDBODBC", "sys", "Cod-2022", { 
    RaiseError => 1,
    AutoCommit => 1
});

# Execute a normal SQL statement to create the users table
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
    warn "Failed to create table: $@";
}

# Insert data
my $insert_stmt = qq{
    INSERT INTO users (id, name, email, age)
    VALUES (?, ?, ?, ?)
};
my $sth = $dbh->prepare($insert_stmt);

eval {
    # Insert the first row of data
    $sth->execute(1, 'Alice', 'alice@example.com', 30);
    
    # Insert the second row of data
    $sth->execute(2, 'Bob', 'bob@example.com', 25);
};
if ($@) {
    warn "Failed to insert data: $@";
}

# Data query module
sub get_data {
    my $select_stmt = qq{
        SELECT id, name, email, age
        FROM users
        WHERE age > ?
    };
    $sth = $dbh->prepare($select_stmt);
    $sth->execute(20);

    print "Query results:\n";
    my $row;
    my $count=0;
    while ($row = $sth->fetchrow_arrayref()) {
        printf "ID: @$row[0], Name: @$row[1], Email: @$row[2], Age: @$row[3]\n";
        $count += 1;
    }
    if ($count == 0) {
        print "no data\n";
    }
}

# Data update module
sub update_data {
    my ($name) = @_;
    my $update_stmt = qq{
        UPDATE users
        SET age = age + 1
        WHERE name = ?
    };
    $sth = $dbh->prepare($update_stmt);
    $sth->execute($name);
    print("Update executed successfully\n");
}

# Data deletion module
sub delete_data {
    my ($email) = @_;
    my $delete_stmt = qq{
        DELETE FROM users
        WHERE email LIKE ?
    };
    $sth = $dbh->prepare($delete_stmt);
    $sth->execute($email);
    print("Deletion executed successfully\n");
}

# Query original data
get_data();

# Query data after update, Alice's age +1
update_data('Alice');
get_data();

# Query data after deletion, empty table
delete_data('%example.com');
get_data();

# Close connection
$sth->finish;
$dbh->disconnect;
```

Run the application in a Linux environment: 

```shell
perl PerlExample.pl
```
