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

Khi bạn sử dụng Reactjs đã có sẵn môi trường Nodejs thì việc cài đặt khá đơn giản

``npm install sanitize-html``

## Sử dụng sanitize-html

Cách sử dụng cũng khá là đơn giản, chỉ cần require

``var sanitizeHtml = require('sanitize-html');``

Và gọi nói thông qua hàm sanitizeHtml
 
``var dirty = 'some really tacky HTML';
var clean = sanitizeHtml(dirty, {});``

Với dirty là nội dung HTML cần render, tham số thứ 2 có thể bỏ trống vì đó là các cài đặt allowed tags và attributes. 
Mặc định thì ``sanitize-html`` đã hỗ trợ một option mặc định như thế này. Đủ để bạn có thể dùng

```javascript
allowedTags: [ 'h3', 'h4', 'h5', 'h6', 'blockquote', 'p', 'a', 'ul', 'ol',
  'nl', 'li', 'b', 'i', 'strong', 'em', 'strike', 'abbr', 'code', 'hr', 'br', 'div',
  'table', 'thead', 'caption', 'tbody', 'tr', 'th', 'td', 'pre', 'iframe' ],
disallowedTagsMode: 'discard',
allowedAttributes: {
  a: [ 'href', 'name', 'target' ],
  // We don't currently allow img itself by default, but this
  // would make sense if we did. You could add srcset here,
  // and if you do the URL is checked for safety
  img: [ 'src' ]
},
// Lots of these won't come up by default because we don't allow them
selfClosing: [ 'img', 'br', 'hr', 'area', 'base', 'basefont', 'input', 'link', 'meta' ],
// URL schemes we permit
allowedSchemes: [ 'http', 'https', 'ftp', 'mailto' ],
allowedSchemesByTag: {},
allowedSchemesAppliedToAttributes: [ 'href', 'src', 'cite' ],
allowProtocolRelative: true,
enforceHtmlBoundary: false
```

Tất nhiên là bạn có thể hoàn toàn chỉnh sửa, bổ sung các quy tắc riêng của bạn.
Ví bạn: Nếu bạn muốn cho phép tất cả thẻ  HTML hoặc tất cả các thuộc tính (attribute) (Tất nhiên không ai khuyến khích bạn làm điều này)

```allowedTags: false,
allowedAttributes: false
```

Hoặc, bạn không muốn bất kỳ Tags hay Attributes nào có thể hoạt động. Set allowedTags to [] and allowedAttributes to {}.

```
allowedTags: [],
allowedAttributes: {}
```

**Nếu bạn muốn chỉ cho phép các giá trị cụ thể trên một số thuộc tính?

```javascript
allowedAttributes: {
  iframe: [
    {
      name: 'sandbox',
      multiple: true,
      values: ['allow-popups', 'allow-same-origin', 'allow-scripts']
    }
  ]
}
```

**Bạn chỉ muốn các iframe của youtube và vimeo có thể hiển thị?


```javascript
clean = sanitizeHtml('<p><iframe src="https://www.youtube.net/embed/nykIhs12345"></iframe><p>', {
  allowedTags: [ 'p', 'em', 'strong', 'iframe' ],
  allowedClasses: {
    'p': [ 'fancy', 'simple' ],
  },
  allowedAttributes: {
    'iframe': ['src']
  },
  allowedIframeHostnames: ['www.youtube.com', 'player.vimeo.com']
});
````

Trên đây là những giới thiệu sơ qua về sanitize-html và cách sử dụng cơ bản. Để có thể tìm hiểu chi tiết hơn. Bạn có thể tham khảo phần document chính thức tại: https://www.npmjs.com/package/sanitize-html
