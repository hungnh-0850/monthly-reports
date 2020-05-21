Thông thường, khi làm việc với các dự án sử dụng Nodejs, đa phần sử dụng CSDL MongoDB bởi sự tiện lợi mà nó mang lại cũng như 
hầu hết các bài hướng dẫn trên Internet đều xoay quanh cặp đôi này.

Tuy nhiên, khi bạn muốn sử dụng CSDL Mysql đã quá quen thuộc thì Nodejs tất nhiên vẫn hỗ trợ điều đó. Đó là lúc chúng ta cần cài đặt thêm MySQL Driver.

Các kết nối và sử dụng như thế nào thì các bạn có thể xem qua bài viết này
https://viblo.asia/p/su-dung-mysql-voi-node-js-express-6J3Zg2mWKmB

Tuy nhiên, mình nhận thấy nếu sử dụng chỉ riêng MySQL driver thì hơi rườm rà, nhiều bước và khó bảo trì project sau này.
Đó là lý do mình đã đi tìm các thư viện hỗ trợ Sql builder hoặc ORM cho Mysql để sử dụng trong Nodejs.

Và tất nhiên, trong số khá khá kết quả thì mình dừng lại ở Knex.js.

Nguyên văn giới thiệu về Knex
>  Knex.js is a "batteries included" SQL query builder for Postgres, MSSQL, MySQL, MariaDB, SQLite3, Oracle, and Amazon Redshift designed to be flexible, portable, and fun to use. It features both traditional node style callbacks as well as a promise interface for cleaner async flow control, a stream interface, full featured query and schema builders, transaction support (with savepoints), connection pooling and standardized responses between different query clients and dialects.



### Cài đặt Knex.js
Sử dụng dòng lệnh sau để cài đặt thư viện Knex.js
```sh
$ npm install knex --save
```
Tất nhiên là phải cài driver cho database tương ứng mà bạn muốn sử dụng. Ở đây mình cần sử dụng Mysql nên sẽ cài đặt mysql driver
thông qua dòng lệnh
```sh
$ npm install mysql
```
Các bạn có thể tham khảo thêm nhiều driver database khác mà Knex hỗ trợ để kết nối, sử dụng.
```sh
$ npm install pg
$ npm install sqlite3
$ npm install mysql
$ npm install mysql2
$ npm install oracledb
$ npm install mssql
```
## Cách sử dụng
### Cấu hình database với Knex.js

Với mysql thì các bạn sử dụng đoạn mã cấu hình sau.
```sh
var knex = require('knex')({
  client: 'mysql',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
});
```
Đối với các loại databse khác thì các bạn xem thêm tại trang chủ của nó. http://knexjs.org/#Installation-node

Tuy nhiên, các bạn có thể nhận thấy là chúng ta chưa tạo được migration hay generate bất kỳ schema nào cho database. 
Có hai loại phương pháp để tạo migrations. 
Cách 1 là tạo theo mẫu của Knex. 
Cách 2 là sử dụng công cụ CLI của Knex.

Và tất nhiên là mình cũng ưu tiên sự tiện lợi nên sẽ không ngại ngùng gì mà chọn cách 2.

Edit package.json file
```sh
"scripts": {
    "dev": "node index.js",
    "knex": "knex",
},
```

Sau đó chạy dòng lệnh để nhìn thấy các dòng lệnh mà Knex' CLI tool hỗ trợ
![S](https://i.imgur.com/x0YWdIh.png)


Đầu tiên, chúng ta cần taọ file knex với tên mặc định knexfile.js bằng cách sử dụng dòng lệnh init
```sh
npm run knex init
```
Bạn sẽ nhận được 1 file knexfile.js với nội dung tương tự sau đây (Mình đã edit và bổ sung 1 số option)

```sh
// knexfile.js
module.exports = {
  development: {
    client: 'mysql',
    connection: {
      host: 'localhost',
      port: 5432,
      user: 'user',
      password: 'password',
      database: 'db_development',
    },
    migrations: {
      directory: './db/migrations',
    },
    seeds: {
      directory: './db/seeds',
    },
  },
};
```
### Tạo bảng dữ liệu với Knex migration
Tiếp đến chúng ta sẽ tạo các file migration

```sh
npm run knex migrate:make products
```

Ở ví dụ trên mình sẽ tạo 1 table có tên là Products. Các bạn sẽ được 1 file dạng như sau


```sh
// migrations
exports.up = function(knex, Promise) {
  return knex.schema.createTableIfNotExists('Products', function(table) {
    table.increments('id')
    table.string('name')
  })
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTableIfExists('Products')
};
```
Để tạo mới 1 database theo thông tin đã config cùng với dữ liệu giống migration thì chúng ta sử dụng dòng lệnh

```sh
npm run knex migrate:latest
```

Xem thêm: http://knexjs.org/#Migrations

### Các queries đến database với Knex

Bây giờ mình sẽ thử viết 1 vài dòng query


```sh
const knex = require('knex');
const knexFile = require('./knexfile').development;
const db = knex(knexFile);

// queryBuilders
const knex = require('knex');
const knexFile = require('./knexfile').development;
const db = knex(knexFile);

class Product {
  static async getById(id) {
    return db
      .first()
      .table('Products')
      .where('id', id)
  }

  static async getByIds(ids) {
    return db
      .select()
      .table('Products')
      .whereIn('id', ids)
  }

  static async getAll() {
    return db
      .select()
      .table('Products')
  }
}

export default Product;
```

Xem thêm các query builder mà Knex hỗ trợ: http://knexjs.org/#Builder


Vậy là mình đã hoàn thành hướng dẫn cách kết nối Nodejs với Mysql bằng cách sử dụng Knex.js library. Hi vọng bài viết này sẽ hữu ích với tất cả các bạn.
Chúc các bạn thành công!
