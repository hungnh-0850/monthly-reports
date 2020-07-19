Bài viết này muốn chia sẻ kiến thức của mình về việc sử dụng các kiểu dữ liệu JSON trong MySQL. 
Tất nhiên, đối với những người đã việc với JSON, chắc chắn rất quen thuộc với loại dữ liệu này, ngoài việc dễ đọc, nó cũng dễ thao tác. 
Việc lựa chọn các kiểu dữ liệu JSON cũng được cho là khá khôn ngoan khi cần thiết. 

Bài viết này dành cho các developer đã hiểu về JSON, cũng như các trường hợp liên quan đến Mã hóa JSON, phân tích cú pháp JSON, làm việc với mảng và tương tự. 
Nếu bạn là một developer, bài viết này có thể không phù hợp vì bạn có thể bối rối khi đọc nội dung.

Bài viết này sẽ đề cập đến 2 table mà chúng ta sẽ làm việc
1. user
2. article

Tính năng mà tôi sẽ cung cấp là: Người dùng có thể đọc bài viết của người dùng khác, cho lượt thích và đánh dấu bài viết.
Nói đến đây, chắc có lẽ bạn sẽ có ý tưởng tạo hai bảng mới, đó là bookmark và like. 
Nhưng trong hướng dẫn này, tôi sẽ giảm số lượng bảng và sử dụng kiểu dữ liệu JSON để lưu dữ liệu. Đó cũng là mục đích chính của bài viết này.

```
- MySQL dump 10.13 Distrib 5.7.23, for Linux (x86_64) 
- 
- Host: 192.168.10.10 Database: my media 
- --------------------- --------------------------------- 
- Server version 5.7.22-0ubuntu18.04.1 

/ *! 40101 SET @ OLD_CHARACTER_SET_CLIENT = @@ CHARACTER_SET_CLIENT * /; 
/ *! 40101 SET @OLD_CHARACTER_SET_RESULTS = @@ CHARACTER_SET_RESULTS * /; 
/ *! 40101 SET @OLD_COLLATION_CONNECTION = @@ COLLATION_CONNECTION * /; 
/ *! 40101 SET NAMES utf8 * /; 
/ *! 40103 SET @OLD_TIME_ZONE = @@ TIME_ZONE * /; 
/ *! 40103 SET TIME_ZONE = '+ 00:00' * /; 
/ *! 40014 SET @OLD_UNIQUE_CHECKS = @@ UNIQUE_CHECKS, UNIQUE_CHECKS = 0 * /; 
/ *! 40014 SET @OLD_FOREIGN_KEY_CHECKS = @@ FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS = 0 * /;
/ *! 40101 SET @OLD_SQL_MODE = @@ SQL_MODE, SQL_MODE = 'NO_AUTO_VALUE_ON_ZERO' * /; 
/ *! 40111 SET @OLD_SQL_NOTES = @@ SQL_NOTES, SQL_NOTES = 0 * /; 

- 
- table structure for `article` table 
- 

DROP TABLE IF EXISTS` article`; 
/ *! 40101 SET @saved_cs_client = @@ character_set_client * /; 
/ *! 40101 SET character_set_client = utf8 * /; 
CREATE TABLE `article` ( 
  ` id` int (11) NOT NULL AUTO_INCREMENT, 
  `user_id` int (11) NOT NULL, 
  ` title` varchar (200) NOT NULL, 
  `content` varchar (45) NOT NULL, 
  ` bookmark` json DEFAULT NULL, 
  `likes` json DEFAULT NULL, 
  PRIMARY KEY (` id`), 
  KEY `fk_owner_idx` (` user_id`),
  CONSTRAINT `fk_owner` FOREIGN KEY (` user_id`) REFERENCES `users` (` id`) ON DELETE NO ACTION ON UPDATE NO ACTION 
) ENGINE = InnoDB AUTO_INCREMENT = 3 DEFAULT CHARSET = latin1; 
/ *! 40101 SET character_set_client = @saved_cs_client * /; 

- 
- dumping data for the `article` table 
- 

LOCK TABLES` article` WRITE; 
/ *! 40000 ALTER TABLE `article` DISABLE KEYS * /; 
INSERT INTO `article` VALUES (1,1, 'Rising Sun', 'Lorem Ipsum', '[]', '[]'), (2.2, 'Shining Again', 'Ipsum Lorem', '[] ',' [] '); 
/ *! 40000 ALTER TABLE `article` ENABLE KEYS * /; 
UNLOCK TABLES; 

- 
- table structure for `users` table 
- 

DROP TABLE IF EXISTS` users`;
/ *! 40101 SET @saved_cs_client = @@ character_set_client * /; 
/ *! 40101 SET character_set_client = utf8 * /; 
CREATE TABLE `users` ( 
  ` id` int (11) NOT NULL AUTO_INCREMENT, 
  `name` varchar (200) NOT NULL, 
  ` email` varchar (200) NOT NULL, 
  `password` varchar (45) NOT NULL, 
  PRIMARY KEY ( 200) `id`) 
) ENGINE = InnoDB AUTO_INCREMENT = 5 DEFAULT CHARSET = latin1; 
/ *! 40101 SET character_set_client = @saved_cs_client * /; 

- 
- dumping data for the `users` table 
- 

LOCK TABLES` users` WRITE; 
/ *! 40000 ALTER TABLE `users` DISABLE KEYS * /;
INSERT INTO `users` VALUES (1, 'Hung', 'huuhung1996@gmail.com', '123456'), (2, 'Tham', 'tham@gmail.com', '1234567'), (3, ' Chien ',' chien@gmail.com ',' 0987654321 '), (4,' Thao ',' thao@gmail.com ',' 098765432 '); 
/ *! 40000 ALTER TABLE `users` ENABLE KEYS * /; 
UNLOCK TABLES; 

- 
- Dumping events for 'my_ media' database 
- 

- 
- Dumping routines for 'my_ media' database 
- 
/ *! 40103 SET TIME_ZONE = @ OLD_TIME_ZONE * /; 

/ *! 40101 SET SQL_MODE = @ OLD_SQL_MODE * /; 
/ *! 40014 SET FOREIGN_KEY_CHECKS = @ OLD_FOREIGN_KEY_CHECKS * /; 
/ *! 40014 SET UNIQUE_CHECKS = @ OLD_UNIQUE_CHECKS * /; 
/ *! 40101 SET CHARACTER_SET_CLIENT = @ OLD_CHARACTER_SET_CLIENT * /; 
/ *! 40101 SET CHARACTER_SET_RESULTS = @ OLD_CHARACTER_SET_RESULTS * /;
/ *! 40101 SET COLLATION_CONNECTION = @ OLD_COLLATION_CONNECTION * /; 
/ *! 40111 SET SQL_NOTES = @ OLD_SQL_NOTES * /; 

- Dump completed on 2020-07-19 21:10:59
```