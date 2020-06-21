Ở bài viết trước, mình đã giới thiệu với các bạn thư viện Knex là 1 SQL query builder giúp tương tác, kết nối, làm viêc với
CSDL 1 cách dễ dàng và hiệu quả. Bài viết đó chỉ dừng lại ở việc giới thiệu cài đặt và thông qua 1 số ví dụ nhỏ

Ở bài viết này, mình sẽ giới thiệu cho các bạn các để Migration và Seeding sử dụng Knex command

# Migration là gì?

Migration là một cách để thực hiện thay đổi hoặc cập nhật cơ sở dữ liệu, như tạo hoặc xóa bảng, cũng như cập nhật bảng với các cột mới với các ràng buộc thông qua các tập lệnh được tạo.
Chúng ta có thể xây dựng các tập lệnh này thông qua dòng lệnh bằng công cụ dòng lệnh Knex (Knex command).

## Tạo hoặc xoá 1 table

Để tạo bảng ``Users`` và `Task` bằng công cụ dòng lệnh Knex. Trong thư mục gốc của dự án, bạn chạy dòng lệnh sau

```
$ knex migrate:make create_users_table
$ knex migrate:make create_tasks_table
```

2 dòng lệnh trên sẽ tự động tạo ra các file migration scripts trong thư mục ./db/migrations với tên chưa timestamp. (Ví dụ:
20201024191043_create_user.js). Mục đích là để Knex phân biệt được file Migration nào là cũ, file nào là mới hơn và thực hiện chúng theo thứ tự.


**20201024191043_create_user.js**

```
exports.up = function(knex, Promise) {
  return knex.schema.createTable('users', function(table) {
    table.increments();
    table.string('email').notNullable();
    table.string('password').notNullable();
    table.timestamp('created_at').defaultTo(knex.fn.now())
    table.timestamp('updated_at').defaultTo(knex.fn.now())
  })
}

exports.down = function(knex, Promise) {
  return knex.schema.dropTable('users');
}
```

**20201024191043_create_task.js**

```
exports.up = function(knex, Promise) {
  return knex.schema.createTable('tasks', function(table) {
    table.increments();
    table.string('title').notNullable();
    table.string('description').notNullable();
    table.boolean('is_complete').notNullable().defaultTo(false);
    table.integer('user_id').references('id').inTable('users');
    table.timestamp('created_at').defaultTo(knex.fn.now());
    table.timestamp('updated_at').defaultTo(knex.fn.now());
  })
}

exports.down = function(knex, Promise) {
  return knex.schema.dropTable('tasks');
}
```
Bây giờ, bạn có thể chạy lệnh dưới đây để thực hiện cập nhật cơ sở dữ liệu cục bộ của mình theo các file Migrations mới nhất đã được thêm vào ở trên.

``` $ knex migrate:latest ```
