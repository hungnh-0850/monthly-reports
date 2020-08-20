Khi làm việc với reactjs, chắc hẳn bạn đã đôi lần muốn hiển thị một nội dung với các thể html. Ví dụ như content của 1 bài viết khi soạn thảo bằng Editor,
hay 1 content của comment có các thẻ ảnh, chèn link trong đó. Bạn quan ngại nếu sử dụng ``dangerouslySetInnerHTML`` trong reactjs thì quá nguy hiểm. 
sẽ dễ dàng bị hack với lỗ hổng XSS phổ biến mà nhiều website mắc phải hiện nay.

Tuy nhiên, việc render các nội dung html này là cần thiết. Vậy để hiển thị chung 1 cách an toàn, sẽ phải làm sao đây?

Bài viết hôm nay sẽ là câu trả lời cho bạn.

Hiện tại, có rất nhiều package hỗ trợ bạn trong việc quản lý, kiểm soát việc hiển thị html một cách an toàn nhất. Tuy nhiên, trong quá trình sử dụng và tìm kiếm qua nhiều package đó, mình xin giới thiệu tới các bạn package ``sanitize-html`` (https://www.npmjs.com/package/sanitize-html)

## Giới thiệu về sanitize-html
sanitize-html cung cấp giải pháp để làm sạch nội dung HTML đơn giản với API rõ ràng. Nó rất thích hợp để làm sạch các đoạn HTML như những đoạn được tạo bởi ckeditor và các trình soạn thảo văn bản khác. Nó cũng đặc biệt tiện dụng để loại bỏ CSS không mong muốn khi sao chép và dán từ Word.


sanitize-html cho phép bạn chỉ định các thẻ HTML bạn muốn cho phép hiển thị và các thuộc tính được phép cho mỗi thẻ đó.

Một số cài đặt mặc định hiệu quả như: Các thuộc tính href được xác thực để đảm bảo chúng chỉ chứa các URL http, https, ftp và mailto. 

## Cài đặt sanitize-html

Việc cài đặt khá đơn giản

``npm install sanitize-html``

## Sử dụng sanitize-html

Cách sử dụng cũng khá là đơn giản, chỉ cần require

``var sanitizeHtml = require('sanitize-html');``

Và gọi nói thông qua hàm sanitizeHtml
 
``var dirty = 'some really tacky HTML';
var clean = sanitizeHtml(dirty, {});``
