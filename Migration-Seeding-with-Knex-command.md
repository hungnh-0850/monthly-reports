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

2 dòng lệnh trên sẽ tự động tạo ra các file migration scripts trong thư mục **./db/migrations** với tên chưa timestamp. (Ví dụ:
**20201024191043_create_user.js**). Mục đích là để Knex phân biệt được file Migration nào là cũ, file nào là mới hơn và thực hiện chúng theo thứ tự.


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

## Thêm mới hoặc xoá columns của bảng đã tồn tại

Giả sử bạn muốn thêm một column mới là fullname đến bảng Users đã tồn tại ở trên,

Tương tự như việc tạo bảng, chúng ta có thể thực hiện việc này bằng cách tạo một tệp migration script khác sẽ được dành riêng để thêm hoặc xóa một cột khỏi bảng mong muốn.

Tạo file mirgation script đó thông qua knex.js bằng dòng lệnh

```$ knex migrate:make add_fullname_to_users```

Bên trong file migration script mới được tạo, bạn có thể chỉnh sửa các hàm export.up và export.down để được như sau

```
exports.up = function(knex, Promise) {
  knex.schema.table('users', function(table) {
    table.integer('fullname').notNull()
  })
}
exports.down = function(knex, Promise) {
  knex.schema.table('users', function(table) {
    table.dropColumn('fullname')
  })
}
```
Bây giờ chúng ta có thể chạy lệnh sau để cập nhật bảng hiện có.
```
$ knex migrate:latest
```
Bạn có thể vào DB để kiểm tra, chắc chắn một cột mới có tên fullname đã được thêm vào bảng **Users**

## Seeding Database

Tương tự như Migration, mô-đun knex cũng cho phép chúng ta tạo các tập lệnh để chèn dữ liệu ban đầu vào các bảng được gọi là Seed file !
Lưu ý rằng nếu chúng ta có quan hệ giữa các bảng, việc seeding cũng phải theo một thứ tự cụ thể.

Ví dụ: chúng ta phải chọn bảng Users để seed trước vì bảng Tasks của chúng ta phải xác thực khóa ngoại là id người dùng đã tồn tại.

```
$ knex seed:make 01_users
$ knex seed:make 02_tasks
```

**01_users.js**

```
exports.seed = function(knex, Promise) {
  // Xoá ALL records có trong db
  return knex('users').del()
  .then(function () {
    // Thêm mới người dùng
    return knex('users').insert([
      {
        id: 1,
        email: 'hung@email.com',
        password: '123456'
      },
      {
        id: 2,
        email: 'tham@email.com',
        password: 'password1'
      },
      {
        id: 3
        email: 'chien@email.com',
        password: 'password123'
      }
    ]);
  });
};
```

**02_tasks.js**

```
exports.seed = function(knex, Promise) {
  // 
  return knex('tasks').del()
  .then(function () {
    // 
    return knex('tasks').insert([
      {
        title: 'Design DB',
        description: 'Design DB',
        is_complete: false,
        user_id: 2
      },
      {
        title: 'Push new code',
        description: 'Push',
        is_complete: false,
        user_id: 1,
      }
    ]);
  });
};
```

Cuối cùng, chạy dòng lệnh sau.

```$ knex seed:run```

Vậy là ở bài viết này, mình đã hoàn tất hướng dẫn các bạn làm việc với Knex command line để Migartions và Seeding. Hi vọng các bạn có thể áp dụng hiệu quả vào dự án.
